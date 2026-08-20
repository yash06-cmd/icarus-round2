DOUMENTATION
Yash Khandelwal
251580051782



Working on the given question began with pulling the docker file and setting up the provided environment so the project could be run locally.
After extracting the workspace I opened the code folder in VScode and used powershell to start the docker container and run the program for the first time to observe its behaviour. Multiple attempts of running and debugging led to my identification of 4 faults.




1.	Sensor copy buffer overflow / canary corruption

I identified this as a warning that didn’t stop the program from running but was a bug nonetheless. The program was putting too much data into a small buffer. The copy length could become larger than the 16 byte destination and overwrite the canary in src/drivers/temp_copy.c.
I added a size check before memcpy so the copy only happens when it fits. The check stops it from overflowing.


2.	Thermal sensor stale reading / scheduler bias 

I identified this bug while looking through the output and noticing the Thermal State binary switch to flip to 1, indicating the bug. When scheduler_bias_flipped was set, thermal_monitor_update() would use the previous temperature instead of the current one in src/drivers/thermal_sensor.c.
I fixed the scheduler/thermal interaction so the thermal reading is handled correctly instead of using the stale value. This was achieved by correcting the scheduler flag handling so the thermal monitor uses the current temperature reading instead of the previous stale value.



3.	Telemetry ring buffer overflow

The telemetry cursor kept moving past the end of the queue in src/drivers/telemetry_ingest.c instead of returning to the beginning.
I added wrapping so it goes back to the first entry when the queue ends.
Basically it was writing past the last box instead of starting again from the first box.


4.	ADC voltage scaling error

The safe switch turned on at tick 1497. In src/drivers/adc_driver.c the ADC value was shifted one extra bit when being converted back to voltage, making the battery appear roughly half as strong. The voltage was being divided by two unnecessarily, which caused the false safe mode condition.
I changed ADC_SHIFT + 1 to ADC_SHIFT.
The voltage was being divided by two unnecessarily, which caused the false safe mode condition.






