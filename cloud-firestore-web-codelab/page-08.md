## 8. Get()'ing data

So far we've shown how to use `onSnapshot` to retrieve updates in realtime, however that's not always what we want. Sometimes it makes more sense to only fetch the data once.

Let's implement the `FriendlyEats.prototype.getRestaurant()` method which is used when you click into a specific restaurant.

#### FriendlyEats.Data.js

```
FriendlyEats.prototype.getRestaurant = function(id) {
  return firebase.firestore().collection('restaurants').doc(id).get();
};
```

Once you've implemented this you'll be able to view pages for each location and leave reviews which will be reflected in the main list.
