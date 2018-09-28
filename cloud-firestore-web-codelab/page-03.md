## 3. Create and set up a Firebase project
### Create Firebase project

- In the [Firebase console](https://console.firebase.google.com/), click `Add project`, then name the Firebase project `FriendlyEats`.
- Click `Create project`.

> **Note:** While your Firebase project will be named **FriendlyEats**, it will be assigned a unique ID in the form **friendlyeats-1234**. This unique identifier is how your project is actually identified, whereas FriendlyEats is simply a display name.

The application that we're going to build uses a few Firebase services available on the web:

- **Firebase Authentication** to easily identify your users
- **Cloud Firestore** to save structured data on the Cloud and get instant notification when the data is updated
- **Firebase Hosting** to host and serve your static assets

Some of these services need special configuration or need to be enabled using the Firebase console.
### Enable Anonymous Auth

Although authentication isn't the focus of this codelab, it's important to have some form of authentication in our app. We'll use **Anonymous login** - this means the user will be silently signed in without being prompted.

You'll need to enable **Anonymous login**.

1. In the Firebase console, open the **Develop** section.
2. Select **Authentication**, then click the **SIGN IN METHOD** tab (or [click here](https://console.firebase.google.com/project/_/authentication/providers) to go directly there).
3. Enable the **Anonymous** Sign-in Provider, then click **SAVE**. This will enable your app to silently sign in your users to the web app.

![](https://codelabs.developers.google.com/codelabs/firestore-web/img/fee6c3ebdf904459.png)

### Enable Cloud Firestore

The app uses Cloud Firestore to save the chat messages and receive new chat messages.

You'll need to enable Cloud Firestore:

1. In the Firebase console's **Develop** section, select **Database**.
2. Click **Create database** in the Cloud Firestore pane.
![](https://codelabs.developers.google.com/codelabs/firestore-web/img/8c5f57293d48652.png)
3. Select the **Start in test mode** option, then click **Enable** after reading the disclaimer about the security rules. This will ensure that we can freely write to the database. We'll make our database more secure later on in this codelab.
![](https://codelabs.developers.google.com/codelabs/firestore-web/img/36bdadf53150d40b.png)