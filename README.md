[ ![Download](https://api.bintray.com/packages/knobtviker/maven/tsl2561/images/download.svg) ](https://bintray.com/knobtviker/maven/tsl2561/_latestVersion)

Taos TSL2561 driver for Android Things
======================================

This driver supports [TSL2561](https://ams.com/tsl2561) ambient light sensor.

It can provide data about luminosity, IR light and visible light.
Works in an environment ranged from a a dark room to high lux sunlight exposure.
Supports CS and FN packages.

How to use the driver
---------------------

### Gradle dependency

To use the `tsl2561` driver, simply add the line below to your project's `build.gradle`,
where `<version>` matches the last version of the driver available on [jcenter](https://bintray.com/knobtviker/maven/tsl2561) .

```
dependencies {
    implementation 'com.knobtviker.android.things.contrib.community.driver:tsl2561:<version>'
}
```

### Sample usage

```java
import com.knobtviker.android.things.contrib.community.driver.tsl2561.TSL2561;

// Access the environmental sensor:

TSL2561 tsl2561;

try {
    tsl2561 = new TSL2561(i2cBusName);
    // Turn on the device and use default gain and integration values
    tsl2561.turnOn();
    tsl2561.setGain();
    tsl2561.setIntegration();
} catch (IOException e) {
    // couldn't configure the device...
}

// Read the current data:
try {
    float luminosity = tsl2561.readLuminosity();
} catch (IOException e) {
    // error reading temperature
}

// Close the environmental sensor when finished:

try {
    tsl2561.close();
} catch (IOException e) {
    // error closing sensor
}
```

If you need to read sensor values continuously, you can register the TSL2561 with the system and
listen for sensor values using the [Sensor APIs](https://developer.android.com/guide/topics/sensors/sensors_overview):
```java
SensorManager mSensorManager = getSystemService(Context.SENSOR_SERVICE);
SensorEventListener mListener = ...;
TSL2561SensorDriver mSensorDriver;

mSensorManager.registerDynamicSensorCallback(new SensorManager.DynamicSensorCallback() {
    @Override
    public void onDynamicSensorConnected(Sensor sensor) {
        if (sensor.getType() == Sensor.TYPE_LIGHT) {
            mSensorManager.registerListener(mListener, sensor,
                    SensorManager.SENSOR_DELAY_NORMAL);
        }
    }
});

try {
    mSensorDriver = new TSL2561SensorDriver(i2cBusName);
    mSensorDriver.registerLuminositySensor();
} catch (IOException e) {
    // Error configuring sensor
}

// Unregister and close the driver when finished:
mSensorManager.unregisterListener(mListener);
mSensorDriver.unregisterLuminositySensor();
try {
    mSensorDriver.close();
} catch (IOException e) {
    // error closing sensor
}
```
