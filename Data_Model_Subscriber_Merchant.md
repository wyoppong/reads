# Data Model for Subscriber and Merchant
## Question
> `A system has subscribers and merchants. A subscriber can become a merchant by registering for the merchant service. A merchant account can also be tied to an existing subscriber account at the time of the merchant account creation. Design and model the database for this system.`

## Answer
To design a database for a system with subscribers and merchants, where subscribers can become merchants and merchants can be tied to existing subscriber accounts, we need to consider the following key points:

1. **Subscribers and Merchants as separate entities with potential overlap**: A subscriber can become a merchant, meaning a subscriber's data should be able to link to merchant data without duplication.
2. **Registration and linking processes**: Subscribers can register to become merchants, and merchants can be linked to subscribers at creation.

Here's a step-by-step guide to designing the database:

### Entities and Relationships
1. **Subscriber**: Represents the users who subscribe to the system.
2. **Merchant**: Represents the users who provide goods/services in the system.
3. **User**: To manage shared attributes and facilitate the transition from subscriber to merchant.

### Tables
1. **Users**: Contains common attributes shared by both subscribers and merchants.
2. **Subscribers**: Contains specific attributes for subscribers.
3. **Merchants**: Contains specific attributes for merchants.

### Table Design
#### 1. Users Table
Stores the basic information common to both subscribers and merchants.

```sql
CREATE TABLE Users (
    user_id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(50) NOT NULL UNIQUE,
    email VARCHAR(100) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

#### 2. Subscribers Table
Stores additional information specific to subscribers.

```sql
CREATE TABLE Subscribers (
    subscriber_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    subscription_date DATE NOT NULL,
    subscription_status VARCHAR(20) NOT NULL,
    FOREIGN KEY (user_id) REFERENCES Users(user_id)
);
```

#### 3. Merchants Table
Stores additional information specific to merchants.

```sql
CREATE TABLE Merchants (
    merchant_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    merchant_name VARCHAR(100) NOT NULL,
    registration_date DATE NOT NULL,
    FOREIGN KEY (user_id) REFERENCES Users(user_id)
);
```

### Example Data Flow
1. **Creating a Subscriber**:
   - Insert data into the `Users` table.
   - Insert corresponding data into the `Subscribers` table, linking to the `Users` table via `user_id`.

2. **Subscriber Becoming a Merchant**:
   - Use the existing `user_id` from the `Users` table.
   - Insert data into the `Merchants` table with the `user_id` from the `Users` table.

3. **Creating a Merchant Linked to a Subscriber**:
   - Insert data into the `Users` table if the user does not exist.
   - Insert data into the `Merchants` table, linking to the existing `user_id` from the `Users` table.

### Example Queries
#### 1. Insert a New Subscriber
```sql
INSERT INTO Users (username, email, password) VALUES ('johndoe', 'john@example.com', 'password123');
INSERT INTO Subscribers (user_id, subscription_date, subscription_status) VALUES (LAST_INSERT_ID(), '2024-05-20', 'active');
```

#### 2. Convert Subscriber to Merchant
```sql
INSERT INTO Merchants (user_id, merchant_name, registration_date) VALUES (existing_user_id, 'John's Store', '2024-05-20');
```

#### 3. Register a New Merchant (who is not already a subscriber)
```sql
INSERT INTO Users (username, email, password) VALUES ('janedoe', 'jane@example.com', 'password123');
INSERT INTO Merchants (user_id, merchant_name, registration_date) VALUES (LAST_INSERT_ID(), 'Jane's Store', '2024-05-20');
```

### Database Schema
This approach ensures:
- **Normalization**: Data is not duplicated, and each piece of information is stored only once.
- **Flexibility**: Easily allows subscribers to become merchants and vice versa.
- **Integrity**: Maintains referential integrity between users, subscribers, and merchants.

By using this design, you can effectively manage subscribers and merchants in a scalable and maintainable way.
