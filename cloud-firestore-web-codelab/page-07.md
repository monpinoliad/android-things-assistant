## 7. Display Data from Cloud Firestore

In this section you will learn how to retrieve data from Cloud Firestore and display it in the app. The two key steps are creating a query and adding a snapshot listener. This listener will be notified of all existing data that matches the query and receive updates in real time.

First, let's construct the query that will serve the default, unfiltered list of restaurants. Put this code in the `FriendlyEats.prototype.getAllRestaurants()` method:

#### FriendlyEats.Data.js

```
 var query = firebase.firestore()
   .collection('restaurants')
   .orderBy('avgRating', 'desc')
   .limit(50);
 this.getDocumentsInQuery(query, render);
```

In this snippet we create a query which will retrieve up to 50 restaurants of the top-level collection named "restaurants" which are ordered by the average rating (currently all zero). Once we've declared this query, we pass it to the `getDocumentsInQuery()` method which is responsible for loading and rendering the data. We'll do this by adding a snapshot listener. Add the following code to the `FriendlyEats.prototype.getDocumentsInQuery()` method:

#### FriendlyEats.Data.js

```
 query.onSnapshot(function(snapshot) {
   if (!snapshot.size) return render();

   snapshot.docChanges().forEach(function(change) {
     if (change.type === 'added') {
       render(change.doc);
     }
   });
 });
```

The `query.onSnapshot` in the code above will trigger its callback every time there is a change to the result of the query. The first time, the callback is triggered with the whole result set of the query so with the whole `restaurants `collection from Cloud Firestore. It then passes all the individual documents to the `render` function. Keep in mind that `change.type` could also be `removed` or `changed` so we need to explicitly say that we're only interested in rendering the new document if it's being added.

Now that we've implemented both methods, refresh the app and verify that the restaurants we saw earlier in console are now visible in the app. If you completed this section successfully your app is now reading and writing data with Cloud Firestore!

As your list of restaurants changes, this listener will keep updating automatically. Try going to the Firebase Console and adding a restaurant by hand - you'll see it show up on your site immediately!

> **Note:** It is also possible to fetch documents from Cloud Firestore once, rather than listening for real time updates using the `Query.get()` method.

![](https://codelabs.developers.google.com/codelabs/firestore-web/img/89e58de6c41cee60.png)