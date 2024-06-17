# Resource Management

Previous section on [memory management] explains the differences between JavaScript and Rust when it comes to GC, ownership and finalizers. It is highly recommended to read it.

This section is limited to providing an example of a fictional _database connection_ involving a SQL connection to be properly closed/disposed/dropped.

```js
class DatabaseConnection {
    constructor(connectionString) {
        this.connectionString = connectionString;
    }

    closeConnection() {
        // Implementation to close the connection
    }
}

// ...closing connection...
DatabaseConnection.prototype.close = function() {
    this.closeConnection();
    console.log(`Closing connection: ${this.connectionString}`);
};

// Create instances of DatabaseConnection
const db1 = new DatabaseConnection("Server=A;Database=DB1");
const db2 = new DatabaseConnection("Server=A;Database=DB2");

// ...code for making use of the database connection...
// "Dispose" of "db1" and "db2" when their scope ends
```

```rust
struct DatabaseConnection(&'static str);

impl DatabaseConnection {
    // ...functions for using the database connection...
}

impl Drop for DatabaseConnection {
    fn drop(&mut self) {
        // ...closing connection...
        self.close_connection();
        // ...printing a message...
        println!("Closing connection: {}", self.0)
    }
}

fn main() {
    let _db1 = DatabaseConnection("Server=A;Database=DB1");
    let _db2 = DatabaseConnection("Server=A;Database=DB2");
    // ...code for making use of the database connection...
} // "Dispose" of "db1" and "db2" called here; when their scope ends
```

<!--In .NET, attempting to use an object after calling `Dispose` on it will typically cause `ObjectDisposedException` to be thrown at runtime. -->In Rust, the compiler ensures at compile-time that attempting to use an object after disposing it will typically cause errors cannot happen.

[memory management]: ../memory-management/index.md
