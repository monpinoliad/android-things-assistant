## 3. Set up the sample project
### Download the code

Run the following command to clone the sample code for this codelab. This will create a folder called `friendlyeats-android` on your machine:

```
$ git clone https://github.com/firebase/friendlyeats-android
```

Import the project into Android Studio. You will probably see some compilation errors or maybe a warning about a missing `google-services.json` file. We'll correct this in the next section.

### Set up Firebase

Follow the steps to [add Cloud Firestore to your Android app](https://firebase.google.com/docs/firestore/quickstart). When prompted for a package name use `com.google.firebase.example.fireeats`. Move the `google-services.json` file into the `app/` folder of the sample code.

Next, in the [Sign-in Providers tab](https://console.firebase.google.com/project/_/authentication/providers) of the Firebase console, enable Email Authentication:

![](https://codelabs.developers.google.com/codelabs/firestore-android/img/2e060b37baf8d29f.png)

Access to data in Cloud Firestore is controlled by Security Rules. We'll talk more about rules later in this codelab but first we need to set some basic rules on our data to get started. In the [Rules tab](https://console.firebase.google.com/project/_/database/firestore/rules) of the Firebase console add the following rules and then click **Publish**.

```
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if request.auth.uid != null;
    }
  }
}
```

The rules above restrict data access to users who are signed in, which prevents unauthenticated users from reading or writing.

### Run the app

If you have set up your app correctly, the project should now compile. In Android Studio click **Build > Rebuild** Project and ensure that there are no remaining errors.

Now run the app on your Android device. At first you will be presented with a "Sign in" screen. You can use an email and password to sign into the app. Once you have completed the sign in process you should see the app home screen:

![](https://codelabs.developers.google.com/codelabs/firestore-android/img/de06424023ffb4b9.png)

In the next section we will add some data to populate the home screen.
