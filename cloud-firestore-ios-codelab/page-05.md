## 5. Display Data from Firestore

In this section you will learn how to retrieve data from Firestore and display it in the app. The two key steps are creating a query and adding a snapshot listener. This listener will be notified of all existing data that matches the query and receive updates in real time.

First, let's construct the query that will serve the default, unfiltered list of restaurants. Take a look at the implementation of `RestaurantsTableViewController.baseQuery()`:

```
return Firestore.firestore().collection("restaurants").limit(to: 50)
```

This query retrieves up to 50 restaurants of the top-level collection named "restaurants". Now that we have a query, we need to attach a snapshot listener to load data from Firestore into our app. Add the following code to the `RestaurantsTableViewController.observeQuery()` method just after the call to `stopObserving()`.

```
listener = query.addSnapshotListener { [unowned self] (snapshot, error) in
  guard let snapshot = snapshot else {
    print("Error fetching snapshot results: \(error!)")
    return
  }
  let models = snapshot.documents.map { (document) -> Restaurant in
    if let model = Restaurant(dictionary: document.data()) {
      return model
    } else {
      // Don't use fatalError here in a real app.
      fatalError("Unable to initialize type \(Restaurant.self) with dictionary \(document.data())")
    }
  }
  self.restaurants = models
  self.documents = snapshot.documents

  if self.documents.count > 0 {
    self.tableView.backgroundView = nil
  } else {
    self.tableView.backgroundView = self.backgroundView
  }

  self.tableView.reloadData()
}
```

The code above downloads collection from Firestore and stores it in an array locally. The `addSnapshotListener(_:)` call adds a snapshot listener to the query that will update the view controller every time the data changes on the server. We get updates automatically and don't have to manually push changes. Remember, this snapshot listener can be invoked at any time as the result of a server-side change so it's important that our app can handle changes.

> **Note:** It is also possible to fetch documents from Firestore once, rather than listening for real time updates, using the [Query.getDocuments(completion:)](https://firebase.google.com/docs/firestore/reference/ios/firestore/api/reference/Classes/FIRQuery#/c:objc(cs)FIRQuery(im)getDocumentsWithCompletion:) method.

After mapping our dictionaries to structs (see `Restaurant.swift`), displaying the data is just a matter of assigning a few view properties. Add the following lines to `RestaurantTableViewCell.populate(restaurant:)` in `RestaurantsTableViewController.swift`.

```
nameLabel.text = restaurant.name
cityLabel.text = restaurant.city
categoryLabel.text = restaurant.category
starsView.rating = Int(restaurant.averageRating.rounded())
priceLabel.text = priceString(from: restaurant.price)
```

This populate method is called from the table view data source's `tableView(_:cellForRowAtIndexPath:)` method, which takes care of mapping the collection of value types from before to the individual table view cells.

Run the app again and verify that the restaurants we saw earlier in console are now visible on the simulator or device. If you completed this section successfully your app is now reading and writing data with Cloud Firestore!

![](https://codelabs.developers.google.com/codelabs/firestore-ios/img/391c0259bf05ac25.png)