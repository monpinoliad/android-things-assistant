## 4. Run the Starter Project
### Run the sample

1. Open Android Studio and select **Import project**.
2. Select the `androidthings-googleassistant directory` from the extracted zip file location.
3. Open the **step1-start-here** module in Android Studio.

### Understanding the starter project

This project contains all of the code you need to have a fully functioning Assistant.

    The solutions to each step of the codelab are organized by folders:  
    - **step1-start-here** — Initial project  
    - **step2-volume-control** — Solution for "Add Volume Control"  
    - **step3-builtin-device-actions** — Solution for "Add "  
    - **step4-device-actions** — Solution for "Add Local Device actions"  
    
    Each folder is implemented as a separate Android Studio module. The **shared** module contains common code for each step.

### Set the DeviceConfig

Before you run the project, you will need to update the app with the device model and instance ids that you registered. Open up `shared/src/main/java/com/example/androidthings/assistant/shared/MyDevice.java`. Update the values of `MODEL_ID` and `INSTANCE_ID`.

The `MODEL_ID` should be the model id created using the Actions Console. The `INSTANCE_ID` should be a unique identifier for each device.

`
public class MyDevice {
    public static final String MODEL_ID = "model-id-from-the-action-console";
    public static final String INSTANCE_ID = "some-identifier-unique-to-your-project";
}
`

4. Deploy the app to the device by selecting **Run → Run 'step1-start-here'** from the menu.

If you have more than one device connected, select the board that start with Iot_ > from the device chooser dialog.

#### Permissions on Android Things
    
    Unlike on regular Android, in Android Things permissions declared in the manifest are granted without requiring the user intervention.  
    
    However, the permissions in the dangerous list [of this document](https://www.google.com/url?q=https://developer.android.com/guide/topics/permissions/overview.html%23dangerous_permissions&sa=D&ust=1523434137580000&usg=AFQjCNHvIiyUScOjASsbJ9xaIe4hSANQxQ), are only granted in the Android Things console, or if use a special flag when installing the app (flag "-g" on "adb install").  
    
    Android Studio 3.0 and later will handle this automatically when it detects that you are installing on an Android Things device, so you only need to add the permission request in AndroidManifest.xml.

5. Select the **Logcat** tab (typically at the bottom of the window) to see the logcat output inside Android Studio.

The project has successfully launched on the device if you can see the following startup message in the log:

`... D AssistantActivity: Starting Assistant demo`

### Send a query

- Press a button to start recording your request

*"What time is it?"*

- Release the button when finished talking.
- The Google Assistant answer should playback on the speaker.

*"It's one thirty."*

### Taking a look under the hood
There are a few key components to make this project work.

#### VoiceHat

The VoiceHat board uses the I2S protocol to read data from the microphones and write audio data to the speaker. This is handled automatically by the framework.

From your activity, you can use the `AudioTrack` and `AudioRecord` classes to interact with audio, just as if a mobile app was using the built-in microphone and speaker. With your driver registered, one can re-use source code and libraries designed for mobile without major changes in your activity.

#### Google Assistant gRPC API

The `AssistantActivity` class has multiple methods which make calls to the Google Assistant by streaming user voice data from the microphones. Each button press triggers a gRPC call through the Assistant SDK.

Local audio data is streamed in chunks to the Assistant, with each chunk wrapped in a [AssistRequest](https://developers.google.com/assistant/sdk/reference/rpc/google.assistant.embedded.v1alpha2#assistrequest). As the audio requests are processed, the activity receives a [AssistResponse](https://developers.google.com/assistant/sdk/reference/rpc/google.assistant.embedded.v1alpha2#assistresponse) for various events. Each response may contain any of the following elements:

- [EventType](https://developers.google.com/assistant/sdk/reference/rpc/google.assistant.embedded.v1alpha2#eventtype)
- [DialogStateOut](https://developers.google.com/assistant/sdk/reference/rpc/google.assistant.embedded.v1alpha2#dialogstateout)
- [AudioOut](https://developers.google.com/assistant/sdk/reference/rpc/google.assistant.embedded.v1alpha2#audioout)

