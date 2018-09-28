## 7. Organize data in subcollections

In this section we'll add ratings to the app so users can review their favorite (or least favorite) restaurants.

### Collections and subcollections

So far we have stored all restaurant data in a top-level collection called "restaurants". When a user rates a restaurant we want to add a new `Rating` object to the restaurants. For this task we will use a subcollection. You can think of a subcollection as a collection that is attached to a document. So each restaurant document will have a ratings subcollection full of rating documents. Subcollections help organize data without bloating our documents or requiring complex queries.

To access a subcollection, call `.collection()` on the parent document:

```
CollectionReference subRef = mFirestore.collection("restaurants")
        .document("abc123")
        .collection("ratings");
```

You can access and query a subcollection just like with a top-level collection, there are no size limitations or performance changes. You can read more about the Firestore data model [here](https://firebase.google.com/docs/firestore/data-model).

### Writing data in a transaction

Adding a `Rating` to the proper subcollection only requires calling `.add()`, but we also need to update the `Restaurant` object's average rating and number of ratings to reflect the new data. If we use separate operations to make these two changes there are a number of race conditions that could result in stale or incorrect data.

To ensure that ratings are added properly, we will use a transaction to add ratings to a restaurant. This transaction will perform a few actions:

- Read the restaurant's current rating and calculate the new one
- Add the rating to the subcollection
- Update the restaurant's average rating and number of ratings

Open `RestaurantDetailActivity.java` and implement the `addRating` function:

```
    private Task<Void> addRating(final DocumentReference restaurantRef, 
                                 final Rating rating) {
        // Create reference for new rating, for use inside the transaction
        final DocumentReference ratingRef = restaurantRef.collection("ratings")
                .document();

        // In a transaction, add the new rating and update the aggregate totals
        return mFirestore.runTransaction(new Transaction.Function<Void>() {
            @Override
            public Void apply(Transaction transaction) 
                    throws FirebaseFirestoreException {

                Restaurant restaurant = transaction.get(restaurantRef)
                        .toObject(Restaurant.class);

                // Compute new number of ratings
                int newNumRatings = restaurant.getNumRatings() + 1;

                // Compute new average rating
                double oldRatingTotal = restaurant.getAvgRating() * 
                        restaurant.getNumRatings();
                double newAvgRating = (oldRatingTotal + rating.getRating()) /
                        newNumRatings;

                // Set new restaurant info
                restaurant.setNumRatings(newNumRatings);
                restaurant.setAvgRating(newAvgRating);

                // Commit to Firestore
                transaction.set(restaurantRef, restaurant);
                transaction.set(ratingRef, rating);

                return null;
            }
        });
    }
```

The `addRating()` function returns a `Task` representing the entire transaction. In the `onRating()` function listeners are added to the task to respond to the result of the transaction.

Now **Run** the app again and click on one of the restaurants, which should bring up the restaurant detail screen. Click the **+** button to start adding a review. Add a review by picking a number of stars and entering some text.

![](https://codelabs.developers.google.com/codelabs/firestore-android/img/78fa16cdf8ef435a.png)

Hitting **Submit** will kick off the transaction. When the transaction completes, you will see your review displayed below and an update to the restaurant's review count:

![](https://codelabs.developers.google.com/codelabs/firestore-android/img/f9e670f40bd615b0.png)

Congrats! You now have a social, local, mobile restaurant review app built on Cloud Firestore. I hear those are very popular these days.
