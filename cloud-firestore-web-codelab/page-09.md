## 9. Sorting and Filtering data

Currently our app displays a list of restaurants, but there is no way for the user to filter based on their needs. In this section you will use Cloud Firestore's advanced querying to enable filtering.

Here's an example of a simple query to fetch all Dim Sum restaurants:

```
var filteredQuery = query.where('category', '==', 'Dim Sum')
```

As its name implies, the `where()` method will make our query download only members of the collection whose fields meet the restrictions we set. In this case, it'll only download restaurants where `category` is `"Dim Sum"`.

In this app the user can chain multiple filters to create specific queries, like "Pizza in San Francisco" or "Seafood in Los Angeles ordered by Popularity".

Take a look at the `FriendlyEats.prototype.getFilteredRestaurants()` method. In this method we'll build up a query which will filter our restaurants based on multiple criteria. Add the following code to the method.

#### FriendlyEats.Data.js

```
var query = firebase.firestore().collection('restaurants');

if (filters.category !== 'Any') {
  query = query.where('category', '==', filters.category);
}

if (filters.city !== 'Any') {
  query = query.where('city', '==', filters.city);
}

if (filters.price !== 'Any') {
  query = query.where('price', '==', filters.price.length);
}

if (filters.sort === 'Rating') {
  query = query.orderBy('avgRating', 'desc');
} else if (filters.sort === 'Reviews') {
  query = query.orderBy('numRatings', 'desc');
}

this.getDocumentsInQuery(query, render);
```

The snippet above adds multiple `where` filters and a single `orderBy` clause to build a compound query based on user input. Now our query will only return restaurants that match the user's requirements.

Run your project and verify you can filter by price, city, and category. While testing you'll see errors in your logs that look like this:

```
The query requires an index. You can create it here: https://console.firebase.google.com/project/.../database/firestore/indexes?create_index=...
```

This is because Cloud Firestore requires indexes for most compound queries. Requiring indexes on queries keeps Cloud Firestore fast at scale. Opening the link from the error message will automatically open the index creation UI in the Firebase console with the correct parameters filled in. To learn more about indexes in Cloud Firestore, [visit the documentation](https://firebase.google.com/docs/firestore/query-data/indexing).
