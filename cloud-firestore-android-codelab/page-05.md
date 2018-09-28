## 5. Display data from Firestore

In this step we will learn how to retrieve data from Firestore and display it in our app. The first step to reading data from Firestore is to create a `Query`. Modify the `initFirestore()` method:

```
    private void initFirestore() {
        mFirestore = FirebaseFirestore.getInstance();

        // Get the 50 highest rated restaurants
        mQuery = mFirestore.collection("restaurants")
                .orderBy("avgRating", Query.Direction.DESCENDING)
                .limit(LIMIT);
    }
```

Now we want to listen to the query, so that we get all matching documents and are notified of future updates in real time. Because our eventual goal is to bind this data to a `RecyclerView`, we need to create a `RecyclerView.Adapter` class to listen to the data.

Open the `FirestoreAdapter` class, which has been partially implemented already. First, let's make the adapter implement `EventListener` and define the `onEvent `function so that it can receive updates to a Firestore query:

```
public abstract class FirestoreAdapter<VH extends RecyclerView.ViewHolder>
        extends RecyclerView.Adapter<VH>
        implements EventListener<QuerySnapshot> {

    // ...

    @Override
    public void onEvent(QuerySnapshot documentSnapshots, 
                        FirebaseFirestoreException e) {
        
        // Handle errors
        if (e != null) {
            Log.w(TAG, "onEvent:error", e);
            return;
        }

        // Dispatch the event
        for (DocumentChange change : documentSnapshots.getDocumentChanges()) {
            // Snapshot of the changed document
            DocumentSnapshot snapshot = change.getDocument();
            
            switch (change.getType()) {
                case ADDED:
                    // TODO: handle document added
                    break;
                case MODIFIED:
                    // TODO: handle document modified
                    break;
                case REMOVED:
                    // TODO: handle document removed
                    break;
            }
        }

        onDataChanged();
    }

  // ...
}
```

On initial load the listener will receive one `ADDED` event for each new document. As the result set of the query changes over time the listener will receive more events containing the changes. Now let's finish implementing the listener. First add three new methods: `onDocumentAdded`, `onDocumentModified`, and on `onDocumentRemoved`:

```
    protected void onDocumentAdded(DocumentChange change) {
        mSnapshots.add(change.getNewIndex(), change.getDocument());
        notifyItemInserted(change.getNewIndex());
    }

    protected void onDocumentModified(DocumentChange change) {
        if (change.getOldIndex() == change.getNewIndex()) {
            // Item changed but remained in same position
            mSnapshots.set(change.getOldIndex(), change.getDocument());
            notifyItemChanged(change.getOldIndex());
        } else {
            // Item changed and changed position
            mSnapshots.remove(change.getOldIndex());
            mSnapshots.add(change.getNewIndex(), change.getDocument());
            notifyItemMoved(change.getOldIndex(), change.getNewIndex());
        }
    }

    protected void onDocumentRemoved(DocumentChange change) {
        mSnapshots.remove(change.getOldIndex());
        notifyItemRemoved(change.getOldIndex());
    }
```

Then call these new methods from `onEvent`:

```
    @Override
    public void onEvent(QuerySnapshot documentSnapshots,
                        FirebaseFirestoreException e) {

        // ...

        // Dispatch the event
        for (DocumentChange change : documentSnapshots.getDocumentChanges()) {
            // Snapshot of the changed document
            DocumentSnapshot snapshot = change.getDocument();

            switch (change.getType()) {
                case ADDED:
                    onDocumentAdded(change);
                    break;
                case MODIFIED:
                    onDocumentModified(change);
                    break;
                case REMOVED:
                    onDocumentRemoved(change);
                    break;
            }
        }
        
        onDataChanged();
    }
```

Finally implement the `startListening()` method to attach the listener:

```
    public void startListening() {
        if (mQuery != null && mRegistration == null) {
            mRegistration = mQuery.addSnapshotListener(this);
        }
    }
```

> **Note:** this codelab demonstrates the real-time capabilities of Firestore, but it's also simple to fetch data without a listener. You can call `get()` on any query or reference to fetch a data snapshot.

Now the app is fully configured to read data from Firestore. **Run** the app again and you should see the restaurants you added in the previous step:

![](https://codelabs.developers.google.com/codelabs/firestore-android/img/9e45f40faefce5d0.png)

Now go back to the Firebase console and edit one of the restaurant names. You should see it change in the app almost instantly!
