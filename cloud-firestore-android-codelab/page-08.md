## 8. Secure your data

At the beginning of this codelab we set our app's security rules to prevent unauthenticated access. In a real application we'd want to set much more fine-grained rules to prevent undesirable data access or modification. Open the rules tab of the console and change the security rules to the following:

> **Note:** rules changes can take 1-5 minutes to take effect.

```
service cloud.firestore {
  match /databases/{database}/documents {
        // Restaurants:
        //   - Authenticated user can read
        //   - Authenticated user can create/update (for demo)
        //   - Validate updates
        //   - Deletes are not allowed
    match /restaurants/{restaurantId} {
      allow read, create: if request.auth.uid != null;
      allow update: if request.auth.uid != null
                    && request.resource.data.name == resource.data.name
      allow delete: if false;
      
      // Ratings:
      //   - Authenticated user can read
      //   - Authenticated user can create if userId matches
      //   - Deletes and updates are not allowed
      match /ratings/{ratingId} {
        allow read: if request.auth.uid != null;
        allow create: if request.auth.uid != null
                      && request.resource.data.userId == request.auth.uid;
        allow update, delete: if false;
        
        }
    }
  }
}
```

These rules restrict access to ensure that clients only make safe changes. For example updates to a restaurant document can only change the ratings, not the name or any other immutable data. Ratings can only be created if the user ID matches the signed-in user, which prevents spoofing.

To read more about Security Rules, visit the [documentation](https://firebase.google.com/docs/firestore/security/secure-data).
