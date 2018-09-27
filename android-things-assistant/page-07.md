## 7. Add Custom Device actions

In addition to talking to the Google Assistant, you may want your device to perform certain actions that are not supported by built-in traits, which may only make sense for your device. To do this, you can use [custom device actions](https://developers.google.com/assistant/sdk/device-actions-overview#custom). These actions can be defined with a custom grammar which may include parameters. When you say a command that your device can support, it will receive a JSON payload that will allow you to handle the query directly. The schema is the same as the EXECUTE payload for [Smart Home devices](https://developers.google.com/actions/smarthome/).

To start with custom device actions, you will need to create an action package which will define all of the query patterns and parameters. In this codelab, you will add a blink action. You will have to specify the ability for it to blink a particular number of times, as well as the frequency which will be defined as a custom type.

Copy the snippet below into a file called `actions.json`.

```
{
  "manifest": {
    "displayName": "Blinky light",
    "invocationName": "Blinky light",
    "category": "PRODUCTIVITY"
  },
  "actions": [
    {
      "name": "com.example.actions.BlinkLight",
      "availability": {
        "deviceClasses": [
          {
            "assistantSdkDevice": {}
          }
        ]
      },
      "intent": {
        "name": "com.example.intents.BlinkLight",
        "parameters": [
          {
            "name": "number",
            "type": "SchemaOrg_Number"
          },
          {
            "name": "speed",
            "type": "Speed"
          }
        ],
        "trigger": {
          "queryPatterns": [
            "blink ($Speed:speed)? $SchemaOrg_Number:number times",
            "blink $SchemaOrg_Number:number times ($Speed:speed)?"
          ]
        }
      },
      "fulfillment": {
        "staticFulfillment": {
          "templatedResponse": {
            "items": [
              {
                "simpleResponse": {
                  "textToSpeech": "Blinking $speed.raw $number times"
                }
              },
              {
                "deviceExecution": {
                  "command": "com.example.commands.BlinkLight",
                  "params": {
                    "speed": "$speed",
                    "number": "$number"
                  }
                }
              }
            ]
          }
        }
      }
    }
  ],
  "types": [
    {
      "name": "$Speed",
      "entities": [
        {
          "key": "slowly",
          "synonyms": [
            "slow"
          ]
        },
        {
          "key": "normally",
          "synonyms": [
            "regular"
          ]
        },
        {
          "key": "quickly",
          "synonyms": [
            "fast",
            "quick"
          ]
        }
      ]
    }
  ]
}
```

There are two query patterns defined:

- `"blink ($Speed:speed)? $SchemaOrg_Number:number times"`
- `"blink $SchemaOrg_Number:number times ($Speed:speed)?"`

These patterns include two parameters. One of these is `$SchemaOrg_Number:number`, which represents the number of times to blink the light. The other is a custom type called `$Speed:speed` which is optional. We define this type as having three entities: slowly, normally, and quickly. There are also synonyms which will match the entities.

When one of the query patterns is said, the fulfillment will be executed. This includes a custom defined phrase and TTS audio. A callback will be sent to the device.

Download the [gactions](https://developers.google.com/actions/tools/gactions-cli) tool, and use it to start testing this action package, replacing `project_id` below with the id for your project.

```
(env) $ gactions test --action_package actions.json --project project_id
```

In the snippet above, you have put this action package in testing mode. Now your device will receive the command `"com.example.commands.BlinkLight"`. You will need to handle this with a new check in the `handleDeviceAction` method.

In order to not block on the main thread, you can use a `Handler` and the `postDelayed` method.

```
public void handleDeviceAction(String command, JSONObject params)
       throws JSONException, IOException {
    if (command.equals("action.devices.commands.OnOff")) {
        mLed.setValue(params.getBoolean("on"));
    } else if (command.equals("com.example.commands.BlinkLight")) {
        int delay = 1000;
        int blinkCount = params.getInt("number");
        String speed = params.getString("speed");
        if (speed.equals("slowly")) {
            delay = 2000;
        } else if (speed.equals("quickly")) {
            delay = 500;
        }
        for (int i = 0; i < blinkCount*2; i++) {
            new Handler(Looper.getMainLooper()).postDelayed(() -> {
                try {
                    mLed.setValue(!mLed.getValue());
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }, i * delay);
        }
    }
}
```

> The test draft will expire in thirty days. Once this happens, you will need to deploy your action package again.

*User: "Blink fast 4 times"*

*Assistant device: <Turns on and off LED>*
