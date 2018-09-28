## 8. Security rules

Users of our app should not be able to read and write every piece of data in our database. For example everyone should be able to see a restaurant's ratings, but only an authenticated user should be allowed to post a rating. It's not sufficient to write good code on the client, we need to specify our data security model on the backend to be completely secure. In this section we'll learn how to use Firebase security rules to protect our data.

First, let's take a deeper look at the security rules we wrote at the start of the codelab. Open the Firebase console and navigate to [Database > Rules in the Firestore tab](https://console.firebase.google.com/project/_/database/firestore/rules).

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

The `request` variable in the rules above is global variable available in all rules, and the conditional we added just ensures that the request is authenticated before allowing it to do anything. This prevents unauthenticated users from using the Firestore API to make unauthorized changes to your data. This is a good start, but we can use Firestore rules to do much more powerful things.

Let's restrict review writes so that the review's user ID must match the ID of the authenticated user. This ensures that users can't impersonate each other and leave fraudulent reviews. Replace your security rules with the following:

```
service cloud.firestore {
  match /databases/{database}/documents {
    match /restaurants/{any}/ratings/{rating} {
      // Users can only write ratings with their user ID
      allow read;
      allow write: if request.auth.uid == request.resource.data.userId;
    }
  
    match /restaurants/{any} {
      // Only authenticated users can read or write data
      allow read, write: if request.auth != null;
    }
  }
}
```

The first match statement matches the subcollection named `ratings` of any document belonging to the `restaurants` collection. The `allow write` conditional then prevents any review from being submitted if the review's user ID doesn't match that of the user. The second match statement allows any authenticated user to read and write restaurants to the database.

This works really well for our reviews, as we've used security rules to explicitly state the implicit guarantee we wrote into our app earlier--that users can only write their own reviews. If we were to add an edit or delete function for reviews, this exact same set of rules would also prevent users from modifying or deleting other users' reviews as well. But Firestore rules can also be used in a more granular fashion to limit writes on individual fields within documents rather than the whole documents themselves. We can use this to allow users to update only the ratings, average rating, and the number of ratings for a restaurant, removing the possibility of a malicious user altering a restaurant name or location.

```
service cloud.firestore {
  match /databases/{database}/documents {
    match /restaurants/{restaurant} {
      match /ratings/{rating} {
        allow read: if request.auth != null;
        allow write: if request.auth.uid == request.resource.data.userId;
      }
    
      allow read: if request.auth != null;
      allow create: if request.auth != null;
      allow update: if request.auth != null
                    && request.resource.data.name == resource.data.name
                    && request.resource.data.city == resource.data.city
                    && request.resource.data.price == resource.data.price
                    && request.resource.data.category == resource.data.category;
    }
  }
}
```

Here we've split up our write permission into create and update so we can be more specific about which operations should be permitted. Any user can write restaurants to the database, preserving the functionality of the Populate button we made at the start of the codelab, but once a restaurant is written its name, location, price, and category cannot be changed. More specifically, the last rule requires any restaurant update operation to maintain the same name, city, price, and category of the already existing fields in the database.

To learn more about what you can do with security rules, take a look at the [documentation](https://firebase.google.com/docs/firestore/security/get-started).
