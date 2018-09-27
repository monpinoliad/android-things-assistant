## 6. Start the virtual device and observe

Add a new tab to your Cloud Shell by clicking the + icon on the menu bar. Navigate to the device sample folder and initialize your virtual environment using the following commands:

```
cd python-docs-samples/iot/api-client/end_to_end_example
source env/bin/activate
```

Retrieve the latest root certificate from Google:

```
wget pki.goog/roots.pem
```

Now, connect the virtual device using the **private** key, registry ID, device ID, and so on:

```
python cloudiot_pubsub_example_mqtt_device.py \
  --registry_id tour-registry \
  --device_id test-dev \
  --project_id "${DEVSHELL_PROJECT_ID:-Cloud Shell}" \
  --private_key_file rsa_private.pem \
  --algorithm RS256 \
  --ca_certs roots.pem \
  --cloud_region us-central1
```

When you connect the device, it will show and report its temperature, which increases when the fan is turned off. If the fan is enabled, the virtual device's temperature will decrease. Because the device is controlled from the server, which is analyzing the stream of incoming sensor data and making this decision for it, the device does not need to be aware of the conditions for enabling or disabling its fan.

> __Note:__ If you are seeing an error indicating "Out of Memory" this usually means that one of the connection parameters is incorrect. Make sure that the `${DEVSHELL_PROJECT_ID:-Cloud Shell}` environment variable is correctly set (Step 4) and check that you are using the correct device ID and registry ID when invoking the sample app.

The following section shows the output of the server that is subscribed to the telemetry events from the device.

```
The device (test-dev) has a temperature of: 11
('Setting fan state for device', u'test-dev', 'to on.')
The device (test-dev) has a temperature of: 10
The device (test-dev) has a temperature of: 9
The device (test-dev) has a temperature of: 8
The device (test-dev) has a temperature of: 7
The device (test-dev) has a temperature of: 6
The device (test-dev) has a temperature of: 5
The device (test-dev) has a temperature of: 4
The device (test-dev) has a temperature of: 3
The device (test-dev) has a temperature of: 2
The device (test-dev) has a temperature of: 1
The device (test-dev) has a temperature of: 0
The device (test-dev) has a temperature of: -1
('Setting fan state for device', u'test-dev', 'to off.')
The device (test-dev) has a temperature of: 0
The device (test-dev) has a temperature of: 1
The device (test-dev) has a temperature of: 2
```

The following section shows the output of the device that is transmitting its telemetry events to the server.

```
('Publishing payload', '{"temperature": 11}')
Published message acked.
Received message '{"fan_on": true}' on topic '/devices/test-dev/config' with Qos 1
Fan turned on.
('Publishing payload', '{"temperature": 10}')
Published message acked.
('Publishing payload', '{"temperature": 9}')
Published message acked.
('Publishing payload', '{"temperature": 8}')
Published message acked.
('Publishing payload', '{"temperature": 7}')
Published message acked.
('Publishing payload', '{"temperature": 6}')
Published message acked.
('Publishing payload', '{"temperature": 5}')
Published message acked.
('Publishing payload', '{"temperature": 4}')
Published message acked.
('Publishing payload', '{"temperature": 3}')
Published message acked.
('Publishing payload', '{"temperature": 2}')
Published message acked.
('Publishing payload', '{"temperature": 1}')
Published message acked.
('Publishing payload', '{"temperature": 0}')
Published message acked.
('Publishing payload', '{"temperature": -1}')
Published message acked.
Received message '{"fan_on": false}' on topic '/devices/test-dev/config' with Qos 1
Fan turned off.
('Publishing payload', '{"temperature": 0}')
Published message acked.
('Publishing payload', '{"temperature": 1}')
Published message acked.
('Publishing payload', '{"temperature": 2}')
```

You have now set up a virtual device and are successfully transmitting telemetry data and receiving configuration changes! At this point you have a basic understanding of Google Cloud IoT Core.

#### Cleaning up

After you're finished with the codelab, run the following commands to clean up the resources you created on your Cloud account:

```
gcloud iot devices delete test-dev --region=us-central1 --registry=tour-registry
gcloud iot registries delete tour-registry --region=us-central1
gcloud pubsub subscriptions delete tour-sub
gcloud pubsub topics delete tour-pub
```
