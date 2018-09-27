## 5. Start the virtual server

Now that you have created all of the Cloud resources you need to connect your device and communicate with it via Pub/Sub, it's time to simulate a device and server.

Edit `cloudiot_pubsub_example_server.py` and replace the code used to generate service account credentials from a provided JSON file to instead use the built-in credentials for Compute Engine, which is what is running under the hood of the Cloud Shell.

Replace the following code:

```
    def __init__(self, service_account_json):
        credentials = ServiceAccountCredentials.from_json_keyfile_name(
            service_account_json, API_SCOPES)
```

With:

```
    def __init__(self, service_account_json):        
        from google.auth import compute_engine
        credentials = compute_engine.Credentials()
```

Now that you have changed the server to use the Compute Engine credentials, start the server using the following syntax:

```
python cloudiot_pubsub_example_server.py \
    --project_id="PROJECT_ID" \
    --pubsub_subscription=PUBSUB_SUBSCRIPTION
```

For example, if you used the example values in previous commands, the command is:

```
python cloudiot_pubsub_example_server.py \
    --project_id="${DEVSHELL_PROJECT_ID:-Cloud Shell}" \
    --pubsub_subscription=tour-sub
```

When the server starts, you will see the message "Listening for messages on projects/your-project-id/subscriptions/tour,"which indicates the server is running.
