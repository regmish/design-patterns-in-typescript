_Singleton Pattern ensures only one instance of a class exists. This is useful when only one instance is needed in our application. e.g Database connection_

### Problem

One of the most popular use case of Singleton is Database connection in our application, as we have only one connection established per application. This pattern can be extended where only one instance of a object is necessary, for. eg.

- All Connections to the third party system (where we don't want to overload with multiple connections)
- Third party libraries, where only one instance is enough/required (e.g logger)

### Solution

```typescript
class DBConnection {
  private static instance: DBConnection;
  // Making a constructor private prohibits calling new DBConnection()
  private constructor() {
    mongoose.connect(process.env.DB_URI, {
      useNewUrlParser: true,
      useUnifiedTopology: true,
      useCreateIndex: true,
      useFindAndModify: false,
    });
  }

  static connection(): DBConnection {
    if (!instance) {
      this.instance = new DBConnection();
    }

    return this.instance;
  }
}
```

How many times we call `DBConnection.connection()`, only one connection to the DB is established.


*In modern programming, Singleton is considered antipattern .It is overused, introduces unnecessary restrictions in situations where a sole instance of a class is not actually required, and introduces global state into an application*
