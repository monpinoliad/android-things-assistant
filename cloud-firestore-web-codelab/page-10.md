## 10. Deploying Indexes

If we don't want to explore every path in our application and follow each of the index creation links, we can easily deploy many indexes at once using the `firebase` command.

In your repo, you'll find a `firestore.indexes.json` file.

#### firestore.indexes.json

```
{
 "indexes": [
   {
     "collectionId": "restaurants",
     "fields": [
       { "fieldPath": "city", "mode": "ASCENDING" },
       { "fieldPath": "avgRating", "mode": "DESCENDING" }
     ]
   },
   ...
 ]
}
```

This file describes all the indexes needed for all the possible combinations of filters.

Deploy these indexes with:

```
firebase deploy --only firestore:indexes
```

After a few minutes, your indexes will be live and the warning messages will go away.
