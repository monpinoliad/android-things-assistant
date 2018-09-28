## 6. Sorting and Filtering data

Currently our app displays a list of restaurants, but there is no way for the user to filter based on their needs. In this section you will use Firestore's advanced querying to enable filtering.

Here's an example of a simple query to fetch all Dim Sum restaurants:

```
let filteredQuery = query.whereField("category", isEqualTo: "Dim Sum")
```

As its name implies, the `whereField(_:isEqualTo:)` method will make our query download only members of the collection whose fields meet the restrictions we set. In this case, it'll only download restaurants where `category` is `"Dim Sum"`.

In this app the user can chain multiple filters to create specific queries, like "Pizza in San Francisco" or "Seafood in Los Angeles ordered by Popularity".

Open `RestaurantsTableViewController.swift` and add the following code block to the middle of `query(withCategory:city:price:sortBy:)`:

```
if let category = category, !category.isEmpty {
  filtered = filtered.whereField("category", isEqualTo: category)
}

if let city = city, !city.isEmpty {
  filtered = filtered.whereField("city", isEqualTo: city)
}

if let price = price {
  filtered = filtered.whereField("price", isEqualTo: price)
}

if let sortBy = sortBy, !sortBy.isEmpty {
  filtered = filtered.order(by: sortBy)
}
```

The snippet above adds multiple `whereField` and `order` clauses to build a single compound query based on user input. Now our query will only return restaurants that match the user's requirements.

Run your project and verify you can filter by price, city, and category (make sure to type the category and city names exactly). While testing you may see errors in your logs that look like this:

```
Error fetching snapshot results: Error Domain=io.grpc Code=9 
"The query requires an index. You can create it here: https://console.firebase.google.com/project/testapp-5d356/database/firestore/indexes?create_index=..." 
UserInfo={NSLocalizedDescription=The query requires an index. You can create it here: https://console.firebase.google.com/project/testapp-5d356/database/firestore/indexes?create_index=...}
```

This is because Firestore requires indexes for most compound queries. Requiring indexes on queries keeps Firestore fast at scale. Opening the link from the error message will automatically open the index creation UI in the Firebase console with the correct parameters filled in. To learn more about indexes in Firestore, [visit the documentation](https://firebase.google.com/docs/firestore/query-data/indexing).
