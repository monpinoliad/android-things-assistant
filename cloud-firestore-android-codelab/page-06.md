## 6. Sort and filter data

The app currently displays the top-rated restaurants across the entire collection, but in a real restaurant app the user would want to sort and filter the data. For example the app should be able to show "Top seafood restaurants in Philadelphia" or "Least expensive pizza".

Clicking white bar at the top of the app brings up a filters dialog. In this section we'll use Firestore queries to make this dialog work:

![](https://codelabs.developers.google.com/codelabs/firestore-android/img/f492d7d83bb11e12.png)

Let's edit the `onFilter()` method of `MainActivity.java`. This method accepts a `Filters` object which is a helper object we created to capture the output of the filters dialog. We will change this method to construct a query from the filters:

```
    @Override
    public void onFilter(Filters filters) {
        // Construct query basic query
        Query query = mFirestore.collection("restaurants");

        // Category (equality filter)
        if (filters.hasCategory()) {
            query = query.whereEqualTo("category", filters.getCategory());
        }

        // City (equality filter)
        if (filters.hasCity()) {
            query = query.whereEqualTo("city", filters.getCity());
        }

        // Price (equality filter)
        if (filters.hasPrice()) {
            query = query.whereEqualTo("price", filters.getPrice());
        }

        // Sort by (orderBy with direction)
        if (filters.hasSortBy()) {
            query = query.orderBy(filters.getSortBy(), filters.getSortDirection());
        }

        // Limit items
        query = query.limit(LIMIT);

        // Update the query
        mQuery = query;
        mAdapter.setQuery(query);

        // Set header
        mCurrentSearchView.setText(Html.fromHtml(filters.getSearchDescription(this)));
        mCurrentSortByView.setText(filters.getOrderDescription(this));

        // Save filters
        mViewModel.setFilters(filters);
    }
```

In the snippet above we build a `Query` object by attaching `where` and `orderBy` clauses to match the given filters.

**Run** the app again and select the following filter to show the most popular low-price restaurants:

![](https://codelabs.developers.google.com/codelabs/firestore-android/img/b5bb1d3ca813f77c.png)

If you view the app logs using `adb logcat` or the **Logcat** panel in Android Studio, you will notice some warnings:

```
W/Firestore Adapter: onEvent:error
com.google.firebase.firestore.FirebaseFirestoreException: FAILED_PRECONDITION: The query requires an index. You can create it here: https://console.firebase.google.com/project/firestore-codelab-android/database/firestore/indexes?create_index=EgtyZXN0YXVyYW50cxoJCgVwcmljZRACGg4KCm51bVJhdGluZ3MQAxoMCghfX25hbWVfXxAD
    at com.google.android.gms.internal.ajs.zze(Unknown Source)
    // ...
```

The query could not be completed on the backend because it requires an index. Most Firestore queries involving multiple fields (in this case price and rating) require a custom index. Clicking the link in the error message will open the Firebase console and automatically prompt you to create the correct index:

![](https://codelabs.developers.google.com/codelabs/firestore-android/img/b7440f99a35be94c.png)

Clicking **Create Index** will begin creating the required index. When the index is complete your query should succeed:

![](https://codelabs.developers.google.com/codelabs/firestore-android/img/d2242654a0fc7a80.png)

> **Note:** the Firestore Android SDK caches documents offline and will return those results in the event of a server error. If you execute a query without a matching index you may still see results in your UI but it's important to check the logs to make sure you have all required indexes.

Now that the proper index has been created, run the app again and execute the same query. You should now see a filtered list of restaurants containing only low-price options:

![](https://codelabs.developers.google.com/codelabs/firestore-android/img/a670188398c3c59.png)

If you've made it this far, you have now built a fully functioning restaurant recommendation app on Firestore! You can now sort and filter restaurants in real time. In the next few sections we add more features and security to the app.
