DOCUMENTATION
(Fault injection)
Yash Khandelwal
251580051782

After debugging, I proceeded with the task of Fault-Injection.
Name of the injected fault: Correlated thermal and vibration structural failure.

On the fault injection branch I added cmd_set_actuators with 100 percent heater power and 5000 RPM wheel speed.At full heater power the temperature increases by 0.20 degrees per tick after accounting for heat loss. The starting temperature was 20 degrees so it reaches 85 degrees at tick 325.
The wheel target was 5000 RPM and the vibration reaches 13 at this speed which is above the critical value of 12.
The wheel reaches 5000 RPM well before tick 325 because it can increase by 50 RPM per tick.Both the temperature and vibration conditions therefore become critical at tick 325
The simulation confirmed this with the crash occurring at tick 325 and exit code 42, which  was already present in the physics code and was not added by me. The predicted crash tick of 325 was then recorded in fault_manifestjson.

