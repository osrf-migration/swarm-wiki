Each vehicle begins with a starting battery capacity. This capacity lowers as time progresses. A vehicle may recharge its battery by stopping (zero velocity) within 100 meters of the base of operations (BOO). Ground vehicles act as recharge stations for rotorcraft. A battery recharges four times faster than it discharges.

Motion, sensor updates, and communication will stop once a battery completely depletes. However, the vehicle's plugin code will still execute.

##Battery Functions

The following functions allow  plugin to get information about the battery.

  1. BatteryStartCapacity() Starting battery capacity (mAh).
  2. BatteryCapacity() Current battery capacity (mAh).
  3. BatteryConsumption() Battery consumption (mA).
  4. BatteryConsumptionFactor() Factor applied to battery consumption to account for additional loss.
  5. ExpectedBatteryLife() Battery life in seconds, based on the current capacity and consumption.

##Battery definition

Each vehicle has a set of parameters, specified in SDF, that define the battery. These parameters include

```
<!-- Capacity in mAh -->
<capacity>110000</capacity>
<!-- Consumption in mA -->
<consumption>55000</consumption>
<!-- A factor that should be between 0 and 1. A value of < 1 can be used
     to account for capacity loss in addition to the <consumption>. -->
<consumption_factor>0.7</consumption_factor>
```

An example implementation is located [here](https://bitbucket.org/osrf/swarm/src/e43ae253f24edff698323dfae1b4a3c90237b2ee/worlds/ground_simple_2.world.erb?at=default&fileviewer=file-view-default#ground_simple_2.world.erb-16)