## 3. Start in the Google Cloud Console

Click the following link to open the end-to-end sample in Google Cloud Shell:

[Open the sample in Google Cloud](https://console.cloud.google.com/cloudshell/open?git_repo=https://github.com/GoogleCloudPlatform/python-docs-samples&page=editor&open_in_editor=iot/api-client/README.md)

If you have already cloned the repo, input 1 to cd into the sample folder and then `cd iot/api-client/end_to_end_example`, otherwise enter the following command to \``cd`\` into the sample folder:

```cd python-docs-samples/iot/api-client/end_to_end_example```

Next, initialize virtual environment and install the sample dependencies:

```
virtualenv env && source env/bin/activate
pip install -r requirements.txt
```

At this point, you can check that you have installed the Python dependencies correctly by running all the Python scripts without passing any parameters:

```
python cloudiot_pubsub_example_mqtt_device.py
python cloudiot_pubsub_example_server.py
```

If the dependencies installed successfully, the programs will print their respective usage messages, for example:

```
usage: cloudiot_pubsub_example_server.py [-h] --project_id PROJECT_ID
                                         --pubsub_subscription
                                         PUBSUB_SUBSCRIPTION
                                         [--service_account_json SERVICE_ACCOUNT_JSON]

usage: cloudiot_pubsub_example_mqtt_device.py [-h] --project_id PROJECT_ID
                                              --registry_id REGISTRY_ID
                                              --device_id DEVICE_ID
                                              --private_key_file
                                              PRIVATE_KEY_FILE --algorithm
                                              {RS256,ES256}
                                              [--cloud_region CLOUD_REGION]
                                              [--ca_certs CA_CERTS]
                                              [--num_messages NUM_MESSAGES]
                                              [--mqtt_bridge_hostname MQTT_BRIDGE_HOSTNAME]
                                              [--mqtt_bridge_port MQTT_BRIDGE_PORT]
                                              [--message_type {event,state}]
```

If you see an error similar to \`ImportError: No module named ...\`, go back and make sure you installed Virtual Environment correctly, or see the [Python Development Environment Setup Guide](https://cloud.google.com/python/setup) for detailed information on using Python with Google Cloud. Now that you have the program libraries installed, it's time to set up your Google Cloud IoT Core project.