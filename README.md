# sql-for-data-science
# assignment 1
--Q1) Retrieve all the records from the Employees table.

SELECT * FROM Employees


--Q2) Retrieve the FirstName, LastName, Birthdate, Address, City, and State from
-- the Employees table.

SELECT FirstName, LastName, Birthdate, Address, City, State 
FROM Employees
WHERE BirthDate = '1965-03-03 00:00:00'


--Q3) Retrieve all the columns from the Tracks table, but only return 20 rows.

select * from Tracks
limit 20

#assignment 2

--Q1) Find all the tracks that have a length of 5,000,000 milliseconds or more.

SELECT COUNT(TrackId)
FROM TRACKS
WHERE Milliseconds >= 5000000


--Q2) Find all the invoices whose total is between $5 and $15 dollars.

SELECT InvoiceID,Total
FROM Invoices
WHERE Total > 5 AND Total < 15


--Q3) Find all the customers from the following States: RJ, DF, AB, BC, CA, WA, NY.

SELECT FirstName, LastName, Company, State
FROM Customers
WHERE State IN ('RJ','DF','AB','BC','CA','WA','NY')


--Q4) Find all the invoices for customer 56 and 58 where the total was between 
-- $1.00 and $5.00.

SELECT CustomerId, InvoiceId, Total, InvoiceDate
FROM Invoices
WHERE CustomerID IN (56,58) AND 
Total BETWEEN 1 AND 5


--Q5) Find all the tracks whose name starts with 'All'.

SELECT TrackId, Name
FROM Tracks
WHERE Name LIKE 'All%'

--Q6) Find all the customer emails that start with "J" and are from gmail.com.

SELECT CustomerId, Email
FROM Customers
WHERE Email LIKE "J%@gmail.com"


--Q7) Find all the invoices from Brasilia, Edmonton, and Vancouver and sort in 
-- descending order by invoice ID.

SELECT InvoiceId, BillingCity, Total
FROM Invoices
WHERE BillingCity IN ('Brasilia','Edmonton','Vancouver')
ORDER BY InvoiceId DESC


--Q8) Show the number of orders placed by each customer and sort the result by
-- the number of orders in descending order.

SELECT CustomerId, COUNT(*) AS Orders
FROM Invoices
GROUP BY CustomerId
ORDER BY Orders DESC

--Q9) Find the albums with 12 or more tracks.

SELECT AlbumId, Count(*) AS Ntracks
FROM Tracks
GROUP BY AlbumId
HAVING COUNT (*) >= 12


#assignment 3

--Q1) Using a subquery, find the names of all the tracks for the
-- album "Californication".

SELECT Name,
       AlbumID
FROM Tracks
WHERE AlbumId IN (SELECT AlbumId
    FROM Albums
    WHERE Title = "Californication");
    
-- Q2) Find the total number of invoices for each customer along 
-- with the customer's full name, city and email.

SELECT FirstName,
       LastName,
       City,
       Email,
       COUNT(I.CustomerId) AS Invoices
FROM Customers C INNER JOIN Invoices I
ON C.CustomerId = I.CustomerId
GROUP BY C.CustomerId

-- Q3) Retrieve the track name, album, artist, and trackID for 
-- all the albums.

SELECT Tracks.Name,
       A.Name AS Artist,
       Albums.Title AS Album,
       Tracks.TrackId
FROM ((Tracks INNER JOIN Albums
ON Tracks.AlbumId = Albums.AlbumId)
INNER JOIN Artists A
ON A.ArtistId = Albums.ArtistId); 


-- Q4) Retrieve a list with the managers last name, and the last 
-- name of the employees who report to him or her.

SELECT M.LastName AS Manager, 
       E.LastName AS Employee
FROM Employees E INNER JOIN Employees M 
ON E.ReportsTo = M.EmployeeID


-- Q5) Find the name and ID of the artists who do not have albums.

SELECT Name AS Artist,
       Artists.ArtistId,
       Albums.Title AS Album
FROM Artists
LEFT JOIN Albums
ON Artists.ArtistId = Albums.ArtistId
WHERE Album IS NULL


-- Q6) Use a UNION to create a list of all the employee's & 
-- customer's first names and last names ordered by the last
-- name in descending order.

SELECT FirstName,
       LastName
FROM Employees
UNION
SELECT FirstName,
       LastName
FROM Customers
ORDER BY LastName DESC


-- Q7) See if there are any customers who have a different city
-- listed in their billing city versus their customer city.

SELECT C.FirstName,
       C.LastName,
       C.City AS CustomerCity,
       I.BillingCity
FROM Customers C
INNER JOIN Invoices I
ON C.CustomerId = I.CustomerId
WHERE CustomerCity != BillingCity

#assignment 4

 Q1) Pull a list of customer ids with the customer’s full name, and address,
along with combining their city and country together. Be sure to make a
 space in between these two and make it UPPER CASE.

SELECT CustomerId,
       FirstName || " " || LastName AS FullName,
       Address,
       UPPER(City || " " || Country) AS CityCountry
FROM Customers


 Q2) Create a new employee user id by combining the first 4 letter of the
 employee’s first name with the first 2 letters of the employee’s last name. 
 Make the new field lower case and pull each individual step to show your work.

SELECT FirstName,
       LastName,
       LOWER(SUBSTR(FirstName,1,4)) AS A,
       LOWER(SUBSTR(LastName,1,2)) AS B,
       LOWER(SUBSTR(FirstName,1,4)) || LOWER(SUBSTR(LastName,1,2)) AS userId
FROM Employees

 Q3) Show a list of employees who have worked for the company for 15 or more 
years using the current date function. Sort by lastname ascending.

SELECT FirstName,
       LastName,
       HireDate,
       (STRFTIME('%Y', 'now') - STRFTIME('%Y', HireDate)) 
          - (STRFTIME('%m-%d', 'now') < STRFTIME('%m-%d', HireDate)) 
          AS YearsWorked
FROM Employees
WHERE YearsWorked >= 15
ORDER BY LastName ASC

 Q4) Profiling the Customers table, answer the following question.

SELECT COUNT(*)
FROM Customers
WHERE [some_column] IS NULL


 Q5) Find the cities with the most customers and rank in descending order.

SELECT City,
       COUNT(*)
FROM Customers
GROUP BY City
ORDER BY COUNT(*) DESC

 Q6) Create a new customer invoice id by combining a customer’s invoice id with
 their first and last name while ordering your query in the following order:
 firstname, lastname, and invoiceID.

SELECT C.FirstName,
       C.LastName,
       I.InvoiceId,
       C.FirstName || C.LastName || I.InvoiceID AS NewId
FROM Customers C INNER JOIN Invoices I
ON C.CustomerId = I.CustomerID
WHERE NewId LIKE 'AstridGruber%'
