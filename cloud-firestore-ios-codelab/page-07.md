## 7. Writing data in a transaction

In this section, we'll add the ability for users to submit reviews to restaurants. Thus far, all of our writes have been atomic and relatively simple. If any of them errored, we'd likely just prompt the user to retry them or retry them automatically.

In order to add a rating to a restaurant we need to coordinate multiple reads and writes. First the review itself has to be submitted, and then the restaurant's rating count and average rating need to be updated. If one of these fails but not the other, we're left in an inconsistent state where the data in one part of our database doesn't match the data in another.

Fortunately, Firestore provides transaction functionality that lets us perform multiple reads and writes in a single atomic operation, ensuring that our data remains consistent.

> **Note:** Transactions are appropriate for complex write operations that also require intermediate reads. If you're just looking to write a bunch of documents at once and don't need to worry about intermediate reads or operation order, you should use [FIRWriteBatch](https://firebase.google.com/docs/firestore/reference/ios/firestore/api/reference/Classes/FIRWriteBatch) instead.

Add the following code below all the let declarations in `RestaurantDetailViewController.reviewController(_:didSubmitFormWithReview:)`.

```
let firestore = Firestore.firestore()
firestore.runTransaction({ (transaction, errorPointer) -> Any? in

  // Read data from Firestore inside the transaction, so we don't accidentally
  // update using stale client data. Error if we're unable to read here.
  let restaurantSnapshot: DocumentSnapshot
  do {
    try restaurantSnapshot = transaction.getDocument(reference)
  } catch let error as NSError {
    errorPointer?.pointee = error
    return nil
  }

  // Error if the restaurant data in Firestore has somehow changed or is malformed.
  guard let restaurant = Restaurant(dictionary: restaurantSnapshot.data()) else {
    let error = NSError(domain: "FireEatsErrorDomain", code: 0, userInfo: [
      NSLocalizedDescriptionKey: "Unable to write to restaurant at Firestore path: \(reference.path)"
    ])
    errorPointer?.pointee = error
    return nil
  }

  // Update the restaurant's rating and rating count and post the new review at the 
  // same time.
  let newAverage = (Float(restaurant.ratingCount) * restaurant.averageRating + Float(review.rating))
      / Float(restaurant.ratingCount + 1)

  transaction.setData(review.dictionary, forDocument: newReviewReference)
  transaction.updateData([
    "numRatings": restaurant.ratingCount + 1,
    "avgRating": newAverage
  ], forDocument: reference)
  return nil
}) { (object, error) in
  if let error = error {
    print(error)
  } else {
    // Pop the review controller on success
    if self.navigationController?.topViewController?.isKind(of: NewReviewViewController.self) ?? false {
      self.navigationController?.popViewController(animated: true)
    }
  }
}
```

Inside of the update block, all the operations we make using the transaction object will be treated as a single atomic update by Firestore. If the update fails on the server, Firestore will automatically retry it a few times. This means that our error condition is most likely a single error occurring repeatedly, for example if the device is completely offline or the user isn't authorized to write to the path they're trying to write to.

> **Note:** When a transaction fails on the server, the update block is also re-executed on each retry. Avoid placing logic that modifies app state inside the update block.
