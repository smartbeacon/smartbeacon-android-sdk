SmartBeacon Android SDK 1.3
=======================

Android framework for iBeacon technology usage.
Min SDK version required: 18


Introduction
--------------------

SmartBeacon's SDK simplifies the use of iBeacon technology with SmartBeacon's hardware. In only few steps, you will be able to communicate with beacons.


Release notes
--------------------

1.3
===
- Server migration (for platform use)
- Fix some bugs
- Javadocs updated

1.2
===
- Platform support. You can now use your SmartBeacon user account (using your api key) to track your users usage
- New listeners added (for platform use): SBPlatformListener and SBPlatformActivityListener
- Fix multiple dex files bug (android.support.v4)

1.1
===
- SBLocationManager.getInstance() need Context object as argument
- Proximities management (IMMEDIATE, NEAR and FAR) 
- Frequency.LOW is 5 seconds
- New method added in SBLocationManagerListener: onUpdatedProximity(SBBeacon beacon, Proximity fromProximity, Proximity toProximity) 
- New SBCalibrator class: you can customize your range of proximities


Integration
--------------------

In few step, you can integrate our SDK, quick an easy.
Follow steps described below to start SmartBeacon SDK implementation:

1. Copy smartbeaconsdk.jar and json-simple  (https://code.google.com/p/json-simple/) into lib directory (in your Android project).

2. Edit your AndroidManifest.xml file by adding following permissions and feature (directly in manifest node):
```java
<!-- Bluetooth Low Energy permissions --->
<uses-permission android:name="android.permission.BLUETOOTH" />
<uses-permission android:name="android.permission.BLUETOOTH_ADMIN" />
<uses-feature android:name="android.hardware.bluetooth_le" android:required="false" />

<!-- Network permissions --->
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="android.permission.INTERNET" />

<!-- Task permission -->
<uses-permission android:name="android.permission.GET_TASKS" />
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
		SBLogger.setSilentMode(false);

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

4. To link your SmartBeacon user account to your Android app, please complete the followings instructions:
```java
// in your activity class, for example
protected void onCreate(Bundle savedInstanceState)
{
	super.onCreate(savedInstanceState);
	
	// get shared instance using Context instance
	SBPlatform platformInstance = SBPlatform.getInstance(this);
	
	// use your own api key
	// (go to account.smartbeacon.eu to get it)
	platformInstance.setApiKey("your_api_key");
	
	// use this listener to customize the behavior of the app when you are catching data/message
	platformInstance.setPlatformListener(this);
	
	// use this listener to visualize your data/message (simple interface)
	platformInstance.setPlatformActivityListener(this);
	
	// if your want to display the dedicated detail activity, please add the declaration of activity in your manifest file
	// inside application tag: <activity android:name="eu.smartbeacon.sdk.platform.SBBeaconActivity"></activity>
	
	// start listening
	platformInstance.startListening();
}

// method to implement when you do: platformInstance.setPlatformListener(...)
@Override
public void onBeaconTriggerInformation(JSONObject info)
{
	// here, you can get 'attached beacon datas' according your SmartBeacon configuration
	//
	// info.get(SBPlatform.DataKey.TITLE); to get message title
	// info.get(SBPlatform.DataKey.DESCRIPTION); to get message description
}

// method to implement when you do: platformInstance.setPlatformActivityListener(...)
@Override
public void onBeaconTriggerActivityIntent(Intent intent)
{
	// here, you can visualize 'attached beacon datas' according your SmartBeacon configuration
	// 
	// startActivity(intent);
	//
	//
	// you can also test if activity is already displayed:
	// get SmartBeacon detail activity full class name:
	// String sbFullClassName = intent.resolveActivity(getPackageManager()).flattenToString();
	//
	// if (ContextManagerUtils.getDisplayedPackageName(context).equals(sbFullClassName))
	// {
	//      don't show activity	
	// }
	// else
	// {
	//      show activity 
	//      startActivity(intent);
	// }
}
	
// don't forget to stop listening
@Override
protected void onDestroy()
{
	// get shared instance using Context instance and stop listening
	SBPlatform.getInstance(this)
			  .stopListening();
		
	super.onDestroy();
}
```
