## 4. Write Data to Firestore

In this section we'll write some data to Firestore so that we can populate the app UI. This can be done manually via the [Firebase console](https://console.firebase.google.com/), but we'll do it in the app itself to demonstrate a basic Firestore write.

The main model object in our app is a restaurant. Firestore data is split into documents, collections, and subcollections. We will store each restaurant as a document in a top-level collection called `"restaurants"`. If you'd like to learn more about the Firestore data model, read about documents and collections in [the documentation](https://firebase.google.com/docs/firestore/data-model).

Before we can add data to Firestore, we need to get a reference to the restaurants collection. Add the following to the inner for loop in the `RestaurantsTableViewController.didTapPopulateButton(_:)` method.

```
let collection = Firestore.firestore().collection("restaurants")
```

Now that we have a collection reference we can write some data. Add the following just after the last line of code we added:

```
let collection = Firestore.firestore().collection("restaurants")

// ====== ADD THIS ======
let restaurant = Restaurant(
  name: name,
  category: category,
  city: city,
  price: price,
  ratingCount: 0,
  averageRating: 0
)

collection.addDocument(data: restaurant.dictionary)
```

The code above adds a new document to the restaurants collection. The document data comes from a dictionary, which we get from a Restaurant struct.

> **Note:** Firestore uses dictionaries and other Foundation data types, but it's best practice in Swift when representing data to use a value type with stronger guarantees than Dictionary. Take a look at Restaurant.swift for the implementations of these value types.

We're almost there--before we can write documents to Firestore we need to open up Firestore's security rules and describe which parts of our database should be writeable by which users. For now, we'll allow only authenticated users to read and write to the entire database. This is a little too permissive for a production app, but during the app-building process we want something relaxed enough so we won't constantly run into authentication issues while experimenting. At the end of this codelab we'll talk about how to harden your security rules and limit the possibility of unintended reads and writes.

Open the Firebase console and navigate to [Database > Rules in the Firestore tab](https://console.firebase.google.com/project/_/database/firestore/rules). The default rules prevent any reads or writes in the database from any user. Replace the defaults with the following rules.

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

We'll discuss security rules in detail later, but if you're in a hurry, take a look at the [security rules documentation](https://firebase.google.com/docs/firestore/security/get-started).

Run the app and sign in. Then tap the "**Populate**" button in the upper left, which will create a batch of restaurant documents, although you won't yet see this in the app.

Next, navigate to the [Firestore data tab](https://console.firebase.google.com/project/_/database/firestore/data) in the Firebase console. You should now see new entries in the restaurants collection:

![](https://codelabs.developers.google.com/codelabs/firestore-ios/img/d0114c2c3ca8d402.png)

Congratulations, you have just written data to Firestore from an iOS app! In the next section you'll learn how to retrieve data from Firestore and display it in the app.
