# Online BookStore
## Project on online bookstore through postgresql
CREATE TABLE Books(
			Book_ID SERIAL PRIMARY KEY,   
			Title VARCHAR(100),
   			Author VARCHAR(100),
			Genre VARCHAR(50),
			Published_Year INT,
			Price NUMERIC(10,2),
			Stock INT
);
CREATE TABLE Customers(
			Customer_ID SERIAL PRIMARY KEY,
			Name VARCHAR(100),
			Email VARCHAR(100),
			Phone VARCHAR(15),
			City VARCHAR(50),
			Country VARCHAR(150)
);
CREATE TABLE Orders(
			Order_ID SERIAl PRIMARY KEY,
			Customer_ID INT REFERENCES Customers(Customer_ID),
			Book_ID INT REFERENCES Books(Book_ID),
			Order_Date DATE,
			Quantity INT, 
			Total_Amount NUMERIC(10,2)
			
);
SELECT * FROM Books;
SELECT * FROM Customers;
SELECT * FROM Orders;

COPY Books(Book_ID,Title,Author,Genre,Published_Year,Price,Stock)
FROM 'C:\Users\jiten\Downloads\Books.csv'
CSV HEADER;

COPY Customers(Customer_ID,Name,Email,Phone,City,Country)
FROM '‪C:\Users\jiten\Downloads\Customers.csv‪‪‪'
CSV HEADER;

COPY Orders(Order_ID,Customer_ID,Book_ID,Order_Date,Quantity,Total_Amount)
FROM 'C:\Users\jiten\Downloads\Orders.csv'
CSV HEADER;

-- 1)Retrieve all the books in the "Fiction" genre:

SELECT * FROM Books
WHERE Genre='Fiction';

--2)Find the books published after the year 1950:

SELECT * FROM Books
WHERE published_year>1950;

--3)List all the customers from canada:

SELECT * FROM Customers
WHERE country='Canada'

--4)Show orders placed in november 2023:

SELECT * FROM Orders
WHERE order_date BETWEEN '2023-11-01'AND '2023-11-30';

--5)Retrieve the total stock of books available:

SELECT SUM(stock) AS Total_Stock FROM Books;

--6)Find the details of most expensive books:

SELECT * FROM Books ORDER BY price DESC LIMIT 1;

--7)Show all customers who ordered more than 1 quantity of book:

SELECT * FROM Orders WHERE quantity>1;

--8)Retrieve all orders where the total amount exceed $20:

SELECT * FROM Orders WHERE total_amount>20

--9)Lists all the genres available in the books:

SELECT DISTINCT genre FROM Books;

--10)Find the book with lowest stock:

SELECT * FROM Books ORDER BY stock LIMIT 1;

--11)Calculate the total revenue generated from all orders:

SELECT SUM(total_amount) AS Revenue FROM Orders;

--ADVANCE QUESTIONS:
--1)Retrieve the total number of books sold for each genre:

SELECT * FROM Orders;

SELECT b.genre,SUM(o.quantity) AS Total_books_sold 
FROM Orders o
JOIN Books b ON o.book_id=b.book_id
GROUP BY b.genre;

--2)Find the average price of the book in the "Fantasy" genre:

SELECT AVG(price) AS average_price
FROM Books
WHERE Genre='Fantasy';

--3)List customers who have placed at least 2 orders:

SELECT o.customer_id,c.name,count(o.order_id) AS Order_Count
FROM Orders o
JOIN customers c ON o.customer_id=c.customer_id
GROUP BY o.customer_id,c.name
HAVING COUNT(order_id)>=2;

--4)Find the most frequently ordered book:

SELECT o.book_id,b.title,COUNT(o.order_id) AS Order_count
FROM Orders o
JOIN Books b ON o.book_id=b.book_id
GROUP BY o.book_id,b.title
ORDER BY Order_count DESC LIMIT 1;

--5)Show the top 3 most expensive books of 'Fantasy'genre:

SELECT * FROM Books
WHERE genre='Fantasy'
ORDER BY price DESC LIMIT 3;

--6)Retrieve the total quantity of book sold by each author:

SELECT b.author,SUM(o.quantity) AS Total_book_sold
FROM Orders o
JOIN Books b ON o.book_id=b.book_id
GROUP BY b.author;

--7)List the cities where customers who spent over $30 are located:

SELECT DISTINCT c.city,total_amount
FROM Orders o
JOIN Customers c ON o.customer_id=c.customer_id
WHERE o.total_amount>30;

--8)Find the customer who spent the most on orders:

SELECT c.customer_id,c.name,SUM(o.total_amount) AS customer_spent
FROM Orders o
JOIN Customers c ON o.customer_id=c.customer_id
GROUP BY c.customer_id,c.name
ORDER BY customer_spent DESC LIMIT 1;

--9)Calculate the stock remaining after fulfilling all orders:

SELECT b.book_id,b.title,b.stock,COALESCE(SUM(quantity),0) AS Order_quantity,
b.stock-COALESCE(SUM(quantity),0) AS remaining_stock
FROM Orders o
LEFT JOIN Books b ON o.book_id=b.book_id
GROUP BY b.book_id ORDER BY b.book_id;

