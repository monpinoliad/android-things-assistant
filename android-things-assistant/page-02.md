## 2. Getting Started
###Update Android SDK

Before you begin building apps for Android Things, you must:

- [Update your SDK tools to version 25.0.3 or higher](https://developer.android.com/studio/intro/update.html#sdk-manager). The updated SDK tools enable you to build and test apps for Things.
- [Update your SDK with Android 7.0 (API 24) or higher](https://developer.android.com/studio/intro/update.html#sdk-manager). The updated platform version provides new APIs for Things apps.

###Flash Android Things

If you have not already installed Android Things on your development board, follow the official [image flashing instructions](https://developer.android.com/things/hardware/raspberrypi.html).
###Assemble the hardware

If you are using the [AIYProjects Voice Kit](https://aiyprojects.withgoogle.com/voice#assembly-guide-1-assemble-the-hardware), follow the assembly guide for the and make sure to use the Android Things SD card flashed in the previous step.

Otherwise, you can get started by connecting the board to power.

> The board may take 30-60 seconds to boot fully into Android Things.
> 
###Connect to the device

1. Verify that Android is running on the device. The Android Things Launcher shows information about the board, including the IP address, if you attach the Raspberry Pi to a monitor.
2. Connect to this IP address using the [adb tool](https://developer.android.com/tools/help/adb.html):

```$ adb connect <ip-address>
connected to <ip-address>:5555```

> Raspberry Pi broadcasts the hostname Android.local over Multicast DNS. If your host platform supports MDNS, you can also connect to the board using the following command:

> ```$ adb connect Android.local```
