## 3. Get the Sample Project
### Download the Code

Begin by cloning the [sample project](https://github.com/firebase/friendlyeats-ios) and running `pod install` in the project directory:

```
git clone https://github.com/firebase/friendlyeats-ios
cd friendlyeats-ios
pod install
```

Open `FireEats.xcworkspace` in Xcode and run it (Cmd+R). The app should compile correctly and immediately crash on launch, since it's missing a `GoogleService-Info.plist file`. We'll correct that in the next step.

### Set up Firebase

Follow [the documentation](https://firebase.google.com/docs/firestore/quickstart) to create a new Firestore project. Once you've got your project, download your project's `GoogleService-Info.plis`t file from [Firebase console](https://console.firebase.google.com/) and drag it to the root of the Xcode project. Run the project again to make sure the app configures correctly and no longer crashes on launch. After logging in, you should see a blank screen like the example below. If you're unable to log in, make sure you've enabled the Email/Password sign-in method in Firebase console under Authentication.

![](https://codelabs.developers.google.com/codelabs/firestore-ios/img/d5225270159c040b.png)
