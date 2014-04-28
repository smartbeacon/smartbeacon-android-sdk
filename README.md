SmartBeacon Android SDK (updated)
=======================

Android framework for iBeacon technology usage.


Introduction
--------------------

SmartBeacon's SDK simplifies the use of iBeacon technology with SmartBeacon's hardware. In only few steps, you will be able to communicate with beacons.


What's new
--------------------

- SBLocationManager.getInstance() need Context object as argument
- Proximities management (IMMEDIATE, NEAR and FAR) 
- Frequency.LOW is 5 seconds
- New method added in SBLocationManagerListener: onUpdatedProximity(SBBeacon beacon, Proximity fromProximity, Proximity toProximity) 
- New SBCalibrator class: you can customize your range of proximities


Integration
--------------------

In few step, you can integrate our SDK, quick an easy.
Follow steps described below to start SmartBeacon SDK implementation:

1. Copy smartbeaconsdk.jar into lib directory (in your Android project).

2. Edit your AndroidManifest.xml file by adding following permissions and feature (directly in manifest node):
```java
<uses-permission android:name="android.permission.BLUETOOTH" />
<uses-permission android:name="android.permission.BLUETOOTH_ADMIN" />
<uses-feature android:name="android.hardware.bluetooth_le" android:required="false" />
```
	
3. Create an activity, DemoActivity for example:
```java
// SmartBeacon imports
import eu.smartbeacon.sdk.core.SBBeacon;
import eu.smartbeacon.sdk.core.SBLocationManager;
import eu.smartbeacon.sdk.core.SBLocationManagerListener;
import eu.smartbeacon.sdk.core.SBRegion;
import eu.smartbeacon.sdk.core.SBScanDeviceListener;
import eu.smartbeacon.sdk.core.SBLocationManager.Frequency;
import eu.smartbeacon.sdk.utils.SBLogger;
	
// other imports
import android.app.Activity;
import android.os.Bundle;
	
public class DemoActivity extends Activity implements SBLocationManagerListener
{
	@Override
	protected void onPostCreate(Bundle savedInstanceState)
	{
		super.onPostCreate(savedInstanceState);

		// enable logging message
		SBLogger.enableLogging(true);

		// get shared instance of SBLocationManager
		SBLocationManager sbManager = SBLocationManager.getInstance(this);

		// add SmartBeacon region
		sbManager.addEntireSBRegion();
		
		// register for beacon location update
		sbManager.addBeaconLocationListener(this);
		
		// you can also change update frequency (update between each update)
		// available values: Frequency.HIGH (eq. 1 sec), Frequency.DEFAULT (eq. 3 sec) and Frequency.LOW (eq. 5 sec)
		//
		// by default, value is Frequency.DEFAULT
		// sbManager.setUpdateFrequency(Frequency.DEFAULT);
				
		// start monitoring and ranging beacons
		sbManager.startMonitoringAllBeaconRegions();
	}
	
	@Override
	protected void onDestroy()
	{
		// stop monitoring and ranging beacons
		SBLocationManager.getInstance(this)
				 .stopMonitoringAllBeaconRegions();
				 
		super.onDestroy();
	}
	
	//
	// these three methods will be called after waiting delay defined by frequency value.
	//
	// called when app enters in range of beacons list
	@Override
	public void onEnteredBeacons(List<SBBeacon> beacons)
	{
		SBLogger.d("we enter into " + beacons.size() + " beacons!");
	}
	
	// called when app exits in range of beacons list
	@Override
	public void onExitedBeacons(List<SBBeacon> beacons)
	{
		SBLogger.d("we leave " + beacons.size() + " beacons!");
	}

	// called when app is in range of beacons list
	@Override
	public void onDiscoveredBeacons(List<SBBeacon> beacons)
	{
		SBLogger.d("we discover " + beacons.size() + " beacons!");
	}
	
	@Override
	public void onUpdatedProximity(SBBeacon beacon, Proximity fromProximity, Proximity toProximity)
	{
		// notification of proximity change for specific beacon
	}
}
```
