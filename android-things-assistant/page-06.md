## 6. Add Built-In Device actions

In addition to talking to the Google Assistant, you may want your device to perform certain actions like turning on and off an LED or setting its brightness. To do this, you can use [built-in device actions](https://developers.google.com/assistant/sdk/device-actions-overview). When you say a command that your device can support, it will receive a JSON payload that will allow you to handle the query directly.

The schema is the same as the EXECUTE payload for [Smart Home devices](https://developers.google.com/actions/smarthome/).

To start with Device actions, you will need to register your device with the types of actions it can support. You have already registered your device in the [Actions Console](http://console.actions.google.com/). Return to your project and open the device model in the **Device registration** section. Click the pencil icon in **Supported traits**.
![](https://codelabs.developers.google.com/codelabs/androidthings-assistant/img/7a7ff9d65d9086df.png)

Select the **OnOff trait** and then press **SAVE**.

Create a new device instance for your updated model using the `INSTANCE_ID` that you added earlier in: `shared/src/main/java/com/example/androidthings/assistant/shared/MyDevice.java`.

You can find your `PROJECT_ID*` in the URL of the Actions console https://console.actions.google.com/project/**project-id**/..., or in the project settings behind the ⚙ button.

```
(env) $ google-oauthlib-tool --client-secrets path/to/credentials.json \
                       --scope https://www.googleapis.com/auth/assistant-sdk-prototype \
                       --save
(env) $ pip install google-assistant-sdk
(env) $ googlesamples-assistant-devicetool --project-id PROJECT_ID list --model
...
(env) $ googlesamples-assistant-devicetool --project-id PROJECT_ID register-device \
--model MODEL_ID --device DEVICE_INSTANCE_ID --client-type SERVICE
...
```

When a Device action command is said, the `AssistResponse` will include a Device action as a serialized JSON payload.

```
@Override
public void onNext(AssistResponse value) {
    // ...
    if (value.getDeviceAction() != null &&
            !value.getDeviceAction().getDeviceRequestJson().isEmpty()) {
        // Iterate through JSON object
        try {
            JSONObject deviceAction = 
                new JSONObject(value.getDeviceAction().getDeviceRequestJson());
            JSONArray inputs = deviceAction.getJSONArray("inputs");
            for (int i = 0; i < inputs.length(); i++) {
                if (inputs.getJSONObject(i).getString("intent")
                        .equals("action.devices.EXECUTE")) {
                    JSONArray commands = inputs.getJSONObject(i)
                        .getJSONObject("payload")
                        .getJSONArray("commands");
                    for (int j = 0; j < commands.length(); j++) {
                        JSONArray execution = commands.getJSONObject(j)
                            .getJSONArray("execution");
                        for (int k = 0; k < execution.length(); k++) {         
                            String command = execution.getJSONObject(k)
                                  .getString("command");
                            JSONObject params = execution.getJSONObject(k)
                                  .optJSONObject("params");
                            handleDeviceAction(command, params);
                        }
                    }
                }
            }
        } catch (JSONException | IOException e) {
            e.printStackTrace();
        }
    }
    // ...
}
```

Create a new method called `handleDeviceAction` which will take the command and parameters and execute the request.

```
public void handleDeviceAction(String command, JSONObject params)
       throws JSONException, IOException {
    if (command.equals("action.devices.commands.OnOff")) {
        mLed.setValue(params.getBoolean("on"));
    }
}    
```

*User: "Turn on"*

*Assistant device: <Turns on LED>*

You can easily change the LED actuated by the device actions to something else, by opening a new Gpio device and referring to the table below:

RED LED (above Button A)    `GPIO2_IO02`  
GREEN LED (above Button B)  `GPIO2_IO00`  
BLUE LED (above Button C)   `GPIO2_IO05`
