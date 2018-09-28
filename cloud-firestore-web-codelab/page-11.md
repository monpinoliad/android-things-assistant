## 11. Writing data in a transaction

In this section, we'll add the ability for users to submit reviews to restaurants. Thus far, all of our writes have been atomic and relatively simple. If any of them errored, we'd likely just prompt the user to retry them or retry them automatically.

In order to add a rating to a restaurant we need to coordinate multiple reads and writes. First the review itself has to be submitted, and then the restaurant's rating count and average rating need to be updated. If one of these fails but not the other, we're left in an inconsistent state where the data in one part of our database doesn't match the data in another.

Fortunately, Cloud Firestore provides transaction functionality that lets us perform multiple reads and writes in a single atomic operation, ensuring that our data remains consistent.

Add the following code in the `FriendlyEats.prototype.addRating()` method.

#### FriendlyEats.Data.js

```
 var collection = firebase.firestore().collection('restaurants');
 var document = collection.doc(restaurantID);

 return document.collection('ratings').add(rating).then(function() {
   return firebase.firestore().runTransaction(function(transaction) {
     return transaction.get(document).then(function(doc) {
       var data = doc.data();

       var newAverage =
         (data.numRatings * data.avgRating + rating.rating) /
         (data.numRatings + 1);

       return transaction.update(document, {
         numRatings: data.numRatings + 1,
         avgRating: newAverage
       });
     });
   });
 });
```

In this block we will first add the review to the `ratings` subcollection of the restaurant. Once this write has succeeded, we'll trigger a transaction to update the numeric values of `averageRating` and `ratingCount` on the restaurant itself.

> **Note:** When a transaction fails on the server, the callback is also re-executed repeatedly. Never place logic that modifies app state inside the transaction callback.
