## 4. Set up the virtual device demo

To ensure your Cloud shell has the latest versions of the Cloud IoT Core API installed, update the gcloud components.

```
gcloud components update
```

    You will need to know the __project id__ value when you run the client and server programs. For the Cloud Shell, the environment variable `${DEVSHELL_PROJECT_ID:-Cloud Shell}` is populated with your current __project id__ after you have set it to your Cloud IoT project.

Open the Google Cloud Console and enable the API if it has not already been enabled on your Kiosk or Google account.

[Enable Cloud IoT Core](https://console.cloud.google.com/iot)

After you have enabled the API, select the current project to get your project ID.

![](https://codelabs.developers.google.com/codelabs/cloud-iot-core-overview/img/884c62f6eb17165.png)

The project name shown in the menu bar can differ from the project ID, so make sure you set it to the correct value.

```
gcloud config set project <your-project-id>
```

Create a Pub/Sub topic using the following gcloud command:

```
gcloud pubsub topics create tour-pub --project="${DEVSHELL_PROJECT_ID:-Cloud Shell}"
gcloud pubsub subscriptions create tour-sub --topic=tour-pub
```

Create your Cloud IoT Device Registry using the following gcloud command:

```
gcloud iot registries create tour-registry \
  --region=us-central1 --event-notification-config=topic=tour-pub
```

Next, you will need to generate RSA public and private keys that will be used for authenticating your virtual device when it connects.


	__Important:__ Your __private__ key should never be transmitted or stored anywhere other than on a device and should only be used by the device to generate an authorization credential.

```
openssl req -x509 -newkey rsa:2048 -days 3650 -keyout rsa_private.pem \
    -nodes -out rsa_public.pem -subj "/CN=unused"
```

With your keys in hand, you're ready to register a device. Register your device using the public key.

```
gcloud iot devices create test-dev --region=us-central1 \
  --registry=tour-registry \
  --public-key path=rsa_public.pem,type=rs256
```

Congratulations, you have now set up Cloud Pub/Sub, created your device registry, and added a device to the registry!