## 6. Write data to Cloud Firestore

In this section, we'll write some data to Cloud Firestore so that we can populate the app's UI. This can be done manually via the [Firebase console](https://console.firebase.google.com/), but we'll do it in the app itself to demonstrate a basic Cloud Firestore write.

The main model object in our app is a restaurant. Cloud Firestore data is split into documents, collections, and subcollections. We'll store each restaurant as a document in a top-level collection called `"restaurants"`. If you'd like to learn more about the Cloud Firestore data model, read about documents and collections in [the documentation](https://firebase.google.com/docs/firestore/data-model).

1. Open `scripts/FriendlyEats.Data.js`.
2. Find the function `FriendlyEats.prototype.addRestaurant`.
3. Replace the entire function with the following code.

#### FriendlyEats.Data.js

```
FriendlyEats.prototype.addRestaurant = function(data) {
  var collection = firebase.firestore().collection('restaurants');
  return collection.add(data);
};
```

The code above adds a new document to the `restaurants` collection. The document data comes from a plain JavaScript object. We do this by first getting a reference to a Cloud Firestore collection `restaurants` then `add`'ing the data.

### Security rules

We're almost there! Before we can write documents to Cloud Firestore, we need to open up Cloud Firestore's security rules and describe which parts of our database should be writeable by which users. For now, we'll allow any authenticated user to read and write to the entire database. This is a little too permissive for a production app, but during the app-building process, we want something relaxed enough so that we won't constantly run into authentication issues while experimenting. At the end of this codelab, we'll talk about how to harden your security rules and limit the possibility of unintended reads and writes.

1. In the Firebase console's `Develop` section, select `Database`.
2. Click `Rules` in the Cloud Firestore tab (or [click here](https://console.firebase.google.com/project/_/database/firestore/rules) to go directly there).
3. Replace the defaults with the following rules.

#### firestore.rules

```
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      // Only authenticated users can read or write data
      allow read, write: if request.auth != null;
    }
  }
}
```

You can simply copy-paste the code above into the Firebase Console, then publish it.

Alternatively, you can use the Firebase CLI to deploy rules to your Firebase project. The [firestore.rules](https://github.com/firebase/friendlyeats-web/blob/master/firestore.rules) file in your project files already contains the rules above. To deploy these rules, run the following command:

```
firebase deploy --only firestore:rules
```

> **Note:** If you'd like to learn more about security rules, have a look at the [security rules documentation](https://firebase.google.com/docs/firestore/security/get-started). You can also have a look at [this alternative firestore.rules](https://github.com/firebase/quickstart-js/blob/master/firestore/firestore.rules) file for FriendlyEats which sets more fine-grained access control.

Refresh the page and tap the "**Add Mock Data**" button, which will create a batch of restaurant documents, although **you won't yet see this in the app**. We still need to implement retrieving the data.

Next, navigate to the [Cloud Firestore tab](https://console.firebase.google.com/project/_/database/firestore/data) in the Firebase console. You should now see new entries in the restaurant's collection:

![](https://codelabs.developers.google.com/codelabs/firestore-web/img/f06898b9d6dd4881.png)

Congratulations, you have just written data to Cloud Firestore from a web app! In the next section you'll learn how to retrieve data from Cloud Firestore and display it in the app.
