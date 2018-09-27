## 3. Set up Assistant SDK Device
### Get the sample

Click the following link to download the sample for this codelab on your development machine:

[Download Source Code](https://github.com/googlecodelabs/androidthings-googleassistant/archive/master.zip)

...or you can clone the GitHub repository from the command line:

```$ git clone https://github.com/googlecodelabs/androidthings-googleassistant.git```

Unpack the downloaded zip file.

### Configure the credentials

1. Enable the following [Activity controls](https://myaccount.google.com/activitycontrols) in the Google Account you plan to use with the Assistant:
	- Web & App Activity
    - Device Information
    - Voice & Audio Activity
2. Open the [Actions Console](http://console.actions.google.com/) and either select an existing project or create a new project.
    - When presented with the list of Action types, select Device Registration at the bottom

3. Select the **Device registration** tab (under **ADVANCED OPTIONS**) from the left navbar.
4. Click the **REGISTER MODEL** button
![Android Things Assistant](https://codelabs.developers.google.com/codelabs/androidthings-assistant/img/18105acf8b9778c.png)
    - Enter your product name, manufacturer name, and select any device type
    - Click the **REGISTER MODEL** button at the bottom of the dialog

5. Click **Download credentials.json** for the client ID to download the client secret JSON file (`credentials.json`).

    - You can skip the section of the dialog where you are asked to select traits. You will return to that later.

6. Enable the [Google Assistant API](https://console.developers.google.com/apis/api/embeddedassistant.googleapis.com/overview) in the Cloud Console
7. Open a terminal on your development machine and follow the instructions to configure a new Python virtual environment.

```
$ python3 -m venv env
$ source env/bin/activate 
(env) $ pip install --upgrade pip setuptools wheel
(env) $ pip install --upgrade google-auth-oauthlib[tool]
```

8. Navigate to your top-level project directory.
9. Use the `google-oauthlib-tool` command line tool to grant permission to use the Assistant API to your application and create a new `credentials.json` file in your app resource directory.


```
(env) $ cd <project-directory-name>
\# Run the tool.
(env) $ google-oauthlib-tool --client-secrets path/to/credentials.json \
                       --credentials shared/src/main/res/raw/credentials.json \
                       --scope https://www.googleapis.com/auth/assistant-sdk-prototype \
                       --save
```
> Replace `path/to/credentials.json` with the path of the JSON file you downloaded in step 6.

This will open a browser and ask you to authorize the application to make request to the assistant on your behalf.

It should then display: `credentials saved: shared/src/main/res/raw/credentials.json`.
