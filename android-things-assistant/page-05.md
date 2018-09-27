## 5. Add Volume Control

The Google Assistant API allows you to control the volume of the Assistant device thru voice with queries like:

*"Turn the volume up"*

*"Turn the volume down"*

*"Set the volume to 4"*

If you try those queries with the starter project, you will notice that the Assistant doesn't understand them yet. You must provide information about the current volume of the device before the Assistant can update it.

You can update the sample to include the current volume percentage of the device in your request, then update the volume of the `AudioTrack` when your app receives the result of the voice query.

Modify the `AssistantActivity` to:

1. Add a new 1mVolumePercentage1 field to the 1AssistantActivity1 class.

`private static int mVolumePercentage = 100;`

2. Update the `AssistConfig` to replace `ASSISTANT_AUDIO_RESPONSE_CONFIG` with a new `AudioOutConfig` containing the new volume parameter. This will be used to establish the current volume, allowing you to change it later.

`
AssistConfig.Builder converseConfigBuilder = AssistConfig.newBuilder()
                    .setAudioInConfig(ASSISTANT_AUDIO_REQUEST_CONFIG)
                    .setAudioOutConfig(AudioOutConfig.newBuilder()
                            .setEncoding(ENCODING_OUTPUT)
                            .setSampleRateHertz(SAMPLE_RATE)
                            .setVolumePercentage(mVolumePercentage)
                            .build())
                    .setDeviceConfig(DeviceConfig.newBuilder()
                            .setDeviceModelId(MyDevice.MODEL_ID)
                            .setDeviceId(MyDevice.INSTANCE_ID)
                            .build());
`

3. In the `mAssistantResponseObserver.onNext(AssistResponse value)` method, handle volume percentage change from the `AssistResult` of incoming `AssistResponse` messages.

4. Use the [AudioTrack.setVolume](https://developer.android.com/reference/android/media/AudioTrack.html#setVolume(float)) method to update the volume of the assistant playback accordingly. The volume must be scaled to be in proportion to the `AudioTrack` limits.

`
private StreamObserver<AssistResponse> mAssistantResponseObserver =
            new StreamObserver<AssistResponse>() {
        @Override
        public void onNext(AssistResponse value) {
            // ...
            if (value.getDialogStateOut() != null) {
                int volume = value.getDialogStateOut().getVolumePercentage();
                if (volume > 0) {
                    mVolumePercentage = volume;
                    Log.i(TAG, "assistant volume changed: " + mVolumePercentage);
                    mAudioTrack.setVolume(AudioTrack.getMaxVolume() * mVolumePercentage / 100.0f);
                }
            }
            // ...

        }
        // ...
    };
`

> This volume will reset whenever you restart the device. If you add [SharedPreferences](https://developer.android.com/training/basics/data-storage/shared-preferences.html) to save this new volume, your volume can persist between power cycles.

Try some volume queries again, they should now modify the volume of the playback of the Assistant:

*"Turn the volume to 2"*

You should see "`assistant volume changed: 20`" in logcat. There will be no verbal response.

*"What sounds does a horse make?"*

The Assistant answer should play back at a very low volume.

*"Turn the volume to maximum"*

You should see "`assistant volume changed: 100`" in logcat.

*"What sounds does a horse make?"*

The Assistant answer should play back at very loud volume.
