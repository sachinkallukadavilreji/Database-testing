PROG8071- DATABASE TESTING - MIDTERM ASSIGNMENT
Team Members
Sachin Kallukadavil Reji – 8877325
Mehak Dhiman – 8974275
Sonam  – 8888725

1. Duties of the individual members
Sachin Kallukavil Reji– Identify tables for an online bookstore and SQL queries to create a sample database with the tables and attributes.
Mehak Dhiman – Writing DDL/DML for one table with CRUD operations.
Sonam – SQL queries for five requirements.
Sachin Kallukavil Reji- Creating a Typescript interface that will allow modification to a table.

## Table creation 

### Customer Table
~~~

CREATE TABLE Customer(
	CustomerID Integer Primary key,
	name Varchar(50),
	address Varchar(50)
);

~~~

### Author Table
~~~

CREATE TABLE Author (
	Author_ID INTEGER Primary key,
	name Varchar(50),
	about Varchar(50)
);

~~~

### Publisher Table
~~~

CREATE TABLE Publisher (
	Publisher_ID Integer Primary key,
	name Varchar(50),
	Contact_number varchar(50),
	email Varchar(50)
);

~~~

### Book table
~~~

CREATE TABLE Book (
	Book_ID INTEGER Primary key,
	name Varchar(50),
	type Varchar(50),
	Author_ID INTEGER,
	Publisher_ID Integer,
	genre Varchar(50),
	rating Decimal (3,2),
	date Date,
	Foreign key (Author_ID) references Author(Author_ID),
	Foreign key (Publisher_ID) references Publisher(Publisher_ID)
);

~~~

### Bill Table
~~~

CREATE TABLE Bill (
	Bill_ID Integer primary key,
	date Date,
	CustomerID Integer,
	amount Decimal(10,2),
	Book_ID Integer,
	Foreign key (CustomerID) references Customer(CustomerID),
	foreign key (Book_ID) references Book(Book_ID)
);

~~~

### Review Table
~~~

CREATE TABLE Review (
	Review_ID Integer Primary key,
	CustomerID Integer,
	Book_ID Integer,
	review_text TEXT,
	rating Integer CHECK (rating >= 1 AND rating <=5),
	Foreign key (CustomerID) references Customer(CustomerID),
	Foreign key (Book_ID) references Book(Book_ID)
);

~~~

### Stock Table

~~~

CREATE TABLE Stock (
	Stock_ID Integer Primary key,
	Book_ID Integer,
	quantity Integer,
	date Date,
	Foreign key (Book_ID) references Book(Book_ID)
);

~~~



## DDL/DML CRUD  OPERATIONS FOR CUSTOMER TABLE

### DDL - CREATE COMMAND

~~~

CREATE TABLE Customer(
	CustomerID Integer Primary key,
	name Varchar(50),
	address Varchar(50)
);

~~~

### DML Insert command

~~~
INSERT INTO Customer (CustomerID, name, address)
VALUES (6, 'Babu', '123 Main');

~~~

### Read command

~~~


Select * from Customers;

~~~

### Delete command

~~~

DELETE FROM Customer
WHERE CustomerID = 6;

~~~

### Update command

~~~

UPDATE Customer
SET address = '456 New Address St'
WHERE CustomerID = 1;

~~~

## Requirements

# Requirements 1 -Power writers (authors) with more than X books in the same genre published within the last X years 
~~~

SELECT 
    author_id, 
    a.name,
    COUNT(Book_ID) AS book_count
FROM 
    Book b
JOIN 
    Author a ON b.author_id = a.Author_ID
WHERE 
    date > CURRENT_DATE - INTERVAL '5 years'
GROUP BY 
    author_id, a.name
HAVING 
    COUNT(Book_ID) > 1;

~~~

### Requirements 2 -Loyal Customers who has spent more than X dollars in the last year
~~~


SELECT 
    c.CustomerID, 
    c.name, 
    c.address, 
    SUM(b.amount) AS total_spent
FROM 
    Customer c
JOIN 
    Bill b ON c.CustomerID = b.CustomerID
WHERE 
    b.date > CURRENT_DATE - INTERVAL '1 year'
GROUP BY 
    c.CustomerID, c.name, c.address
HAVING 
    SUM(b.amount) > 60;


~~~

### Requirements 3 - Well Reviewed books that has a better user rating than average

~~~

WITH AverageRating AS (
    SELECT AVG(rating) AS avg_rating
    FROM Book
)
SELECT 
    b.Book_ID, 
    b.name, 
    b.genre, 
    b.rating, 
    a.name AS author_name
FROM 
    Book b
JOIN 
    Author a ON b.Author_ID = a.Author_ID
JOIN 
    AverageRating ar ON b.rating > ar.avg_rating
ORDER BY 
    b.rating DESC;


~~~

### Requirements 4 -The most popular genre by sales
~~~

WITH AverageRating AS (
    SELECT AVG(rating) AS avg_rating
    FROM Book
)
SELECT 
    b.Book_ID, 
    b.name, 
    b.genre, 
    b.rating, 
    a.name AS author_name
FROM 
    Book b
JOIN 
    Author a ON b.Author_ID = a.Author_ID
JOIN 
    AverageRating ar ON b.rating > ar.avg_rating
ORDER BY 
    b.rating DESC;


~~~

### Requirements 5 - The 10 most recent posted reviews by Customers
~~~

SELECT 
    r.Review_ID, 
    r.review_text, 
    r.rating, 
    c.name AS customer_name, 
    b.name AS book_name, 
    r.date
FROM 
    Review r
JOIN 
    Customer c ON r.CustomerID = c.CustomerID
JOIN 
    Book b ON r.Book_ID = b.Book_ID
ORDER BY 
    r.date DESC
LIMIT 10;

~~~

### Typescript interface for update operation

~~~
import { Client } from 'pg';

// Database connection configuration
const client = new Client({
    host: "127.0.0.1",
    user: "postgres",
    port: 5432,
    password: "new_password",
    database: "Online_BookStore",
    ssl: false // Optionally disable SSL
});

// Function to update an author's first name and last name
async function updateAuthor(authorId: number, newFirstName: string, newLastName: string): Promise<void> {
    try {
        await client.connect();
        const query = 'UPDATE Authors SET FirstName = $1, LastName = $2 WHERE AuthorID = $3';
        const values = [newFirstName, newLastName, authorId];

        const res = await client.query(query, values);
        console.log(Author with ID ${authorId} updated successfully. New name: ${newFirstName} ${newLastName});
    } catch (err) {
        console.error('Error updating author:', err.message);
    } finally {
        await client.end();
    }
}

// Example usage: Update author with ID 1 to "David" as first name and "Aorne" as last name
const newFirstName = 'Jane';
const newLastName = 'Aorne';
updateAuthor(1, newFirstName, newLastName);

~~~
