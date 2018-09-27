## 5. Create a database and schema

In this step we're going to create our sample database and schema.

Click on "Create database" on the Instance Details screen that followed the creation of the instance or select your instance from the instance list when you select the Spanner menu item.

![](https://codelabs.developers.google.com/codelabs/cloud-spanner-first-steps-java/img/c1f27166e3883512.png)

![](https://codelabs.developers.google.com/codelabs/cloud-spanner-first-steps-java/img/3db0361dd1be74c7.png)

Follow the guided dialog to create the database and schema.

![](https://codelabs.developers.google.com/codelabs/cloud-spanner-first-steps-java/img/45a4351cd772b97d.png)

After clicking "Continue" we will create a schema with a DDL. For this step, switch the toggle "Edit as text" and paste the following DDL and click "Create".

```
CREATE TABLE Songs (
   SongId  STRING(36) NOT NULL,
   Title   STRING(100), 
   year    INT64, 
   peak    INT64,
) PRIMARY KEY(SongId);
```

![](https://codelabs.developers.google.com/codelabs/cloud-spanner-first-steps-java/img/a6c99b3add3f0ba1.png)

After a couple seconds you will see your new database and table coming up in the left hand-side menu.

![](https://codelabs.developers.google.com/codelabs/cloud-spanner-first-steps-java/img/a65db716a4ed1e11.png)

In the next step we will use the Java client library to load some data into your new database.
