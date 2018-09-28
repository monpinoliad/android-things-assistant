## 4. Write data to Firestore

In this section we will write some data to Firestore so that we can populate the home screen. You can enter data manually in the [Firebase console](https://console.firebase.google.com/), but we'll do it in the app itself to demonstrate how to write data to Firestore using the Android SDK.

The main model object in our app is a restaurant (see `model/Restaurant.java`). Firestore data is split into documents, collections, and subcollections. We will store each restaurant as a document in a top-level collection called `"restaurants"`. To learn more about the Firestore data model, read about documents and collections in [the documentation](https://firebase.google.com/docs/firestore/data-model).

First, let's get an instance of `FirebaseFirestore` to work with. Edit the `initFirestore()` method in `MainActivity`:

```
    private void initFirestore() {
        mFirestore = FirebaseFirestore.getInstance();
    }
```

For demonstration purposes, we will add functionality in the app to create ten random restaurants when we click the "Add Random Items" button in the overflow menu. Fill in the `onAddItemsClicked()`:

```
    private void onAddItemsClicked() {
        // Get a reference to the restaurants collection
        CollectionReference restaurants = mFirestore.collection("restaurants");

        for (int i = 0; i < 10; i++) {
            // Get a random Restaurant POJO
            Restaurant restaurant = RestaurantUtil.getRandom(this);

            // Add a new document to the restaurants collection
            restaurants.add(restaurant);
        }
    }
```

There are a few important things to note about the code above:

- We started by getting a reference to the `"restaurants"` collection. Collections are created implicitly when documents are added, so there was no need to create the collection before writing data.
- Documents can be created using POJOs, which we use to create each Restaurant doc.
- The `add()` method adds a document to a collection with an auto-generated ID, so we did not need to specify a unique ID for each Restaurant.

Now run the app again and click the "Add Random Items" button in the overflow menu to invoke the code you just wrote:

![](https://codelabs.developers.google.com/codelabs/firestore-android/img/419594cde6e1adc1.png)

If you navigate to the [Firebase console](https://console.firebase.google.com/project/_/database/firestore/data), you should see the newly added data:

![](https://codelabs.developers.google.com/codelabs/firestore-android/img/bf51b67b99a725a3.png)

Congratulations, you just wrote data to Firestore! In the next step we'll learn how to display this data in the app.
