# 🎵 Digital Music Store SQL Project

## Project Overview

**Project Title**: Music Store SQL Analysis              
**Database**: `Music_Store_Database.sql`

This project contains a set of SQL queries designed to analyze a Music Store Database.
The queries cover business questions such as customer behavior, revenue insights, and artist popularity.

📂 Project Structure

- `music_store_analysis.sql` → Contains all SQL queries (numbered and explained).
- `README.md` → Documentation of the project.
- `schema_diagram.png` → Database schema diagram (for reference).
- `excel_files` → Folder containing Excel files.

## 📊 Queries Overview
Here are the 13 business questions solved in this project:

1. Senior-most employee – Find the top-level employee based on job title.
2. Top 3 invoice countries – Countries with the most invoices.
3. Media type revenue – Which media type generated the highest revenue.
4. Employee support load – Employees and number of customers they support.
5. Top 3 invoice values – Biggest invoices recorded.
6. Album revenue – Revenue per album with artist names.
7. Best customer city – City generating the most revenue.
8. Best customer – Customer who spent the most.
9. Rock listeners – Emails and details of Rock music listeners.
10. Top 10 Rock artists – Artists who wrote the most Rock tracks.
11. Tracks above average length – Longest songs compared to average.
12. Customer spending on artists – Breakdown of how much each customer spent per artist.
13. Most popular genre per country – Top genre in each country by purchases.


## 📝 Detailed Queries
1. **Who is the senior most employee based on job title?**

```sql
SELECT * FROM employee
ORDER BY levels DESC
LIMIT 1
```


2. **Which top 3 countries have the most Invoices?**

```sql
SELECT billing_country, count(total) 
AS total_invoices FROM invoice
GROUP BY billing_country 
ORDER BY total_invoices DESC
LIMIT 3;
```

3. **Find out which media type has generated the highest revenue. Return the media type name and the total revenue.**

```sql
SELECT 
	mt.name, SUM(il.unit_price * il.quantity) AS total_revenue 
FROM media_type mt 
INNER JOIN track t 
	ON t.media_type_id= mt.media_type_id
INNER JOIN invoice_line il 
	ON  il.track_id=t.track_id 
GROUP BY mt.name 
ORDER BY SUM(il.unit_price * il.quantity) DESC LIMIT 1;
```

4. **List the employees and the total number of customers they support (support_rep_id). Return employee full name and total customers,ordered by number of customers descending.**

```sql
SELECT 
    e.first_name || ' ' || e.last_name AS employee_name,
    COUNT(c.customer_id) AS total_customers
FROM employee e
JOIN customer c 
    ON e.employee_id = c.support_rep_id
GROUP BY employee_name
ORDER BY total_customers DESC;
```

5. **What are top 3 values of total invoice?**

```sql
SELECT 
	total FROM invoice 
ORDER BY total DESC LIMIT 3;
```

6. **Write a query that returns the album title, the artist name, and the total revenue that album has generated. Order the result by revenue in descending order.**

```sql
SELECT 
	ab.title, a.name,SUM(il.unit_price*il.quantity) AS total_revenue 
	FROM artist a 
INNER JOIN album ab 
	ON a.artist_id=ab.artist_id 
INNER JOIN track t 
	ON t.album_id=ab.album_id 
INNER JOIN invoice_line il 
	ON t.track_id=il.track_id
	GROUP BY ab.album_id,ab.title,a.name
ORDER BY total_revenue DESC;
```


7. **Which city has the best customers? We would like to throw a promotional Music Festival in the city we made the most money. Write a query that returns one city that has the highest sum of invoice totals. Return both the city name & sum of all invoice totals**

```sql
SELECT 
	billing_city, SUM(total) AS total 
	FROM invoice
GROUP BY billing_city
	ORDER BY total DESC LIMIT 1;
```


8. **Who is the best customer? The customer who has spent the most money will be declared the best customer. Write a query that returns the person who has spent the most money.**

```sql
SELECT 
	c.first_name||' '||c.last_name AS customer_name,
SUM(i.total) AS total FROM customer c 
INNER JOIN invoice i 
	on i.customer_id=c.customer_id 
GROUP BY customer_name 
ORDER BY total DESC LIMIT 1;
```


9. **Write query to return the email, first name, last name, & Genre of all Rock Music listeners. Return your list ordered alphabetically by email starting with A**

```sql
SELECT 
	DISTINCT c.email, c.first_name, c.last_name, g.name
	FROM customer c 
INNER JOIN invoice i 
	ON i.customer_id=c.customer_id 
INNER JOIN invoice_line il 
	ON il.invoice_id=i.invoice_id 
INNER JOIN track t 
	ON t.track_id=il.track_id 
INNER JOIN genre g 
	ON g.genre_id=t.genre_id
WHERE g.name = 'Rock' ORDER BY c.email;
```

10. **Let's invite the artists who have written the most rock music in our dataset. Write a query that returns the Artist name and total track count of the top 10 rock bands**


```sql
SELECT 
	a.name, COUNT(t.track_id) AS track_count FROM artist a 
INNER JOIN album ab 
	ON a.artist_id=ab.artist_id 
INNER JOIN track t 
	ON ab.album_id=t.album_id 
INNER JOIN genre g 
	ON g.genre_id=t.genre_id 
WHERE g.name='Rock'
	GROUP BY a.name
ORDER BY track_count DESC LIMIT 10;
```

11. **Return all the track names that have a song length longer than the average song length. Return the Name and Milliseconds for each track. Order by the song length with the longest songs listed first.**

```sql
SELECT 
name, milliseconds 
FROM track WHERE milliseconds>(SELECT AVG(milliseconds) AS avg_track_lenth FROM track) 
ORDER BY milliseconds DESC;
```


12. **Find how much amount spent by each customer on artists? Write a query to return customer name, artist name and total spent.**

```sql
WITH cte_customer_artist_spend AS (
    SELECT 
        c.customer_id,
        c.first_name|| ' '|| c.last_name AS customer_name,
        ar.artist_id,
        ar.name AS artist_name,
        il.unit_price * il.quantity AS amount_spent
    FROM customer c
INNER JOIN invoice i 
      ON c.customer_id = i.customer_id
INNER JOIN invoice_line il 
      ON i.invoice_id = il.invoice_id
INNER JOIN track t 
      ON il.track_id = t.track_id
INNER JOIN album al 
      ON t.album_id = al.album_id
INNER JOIN artist ar 
      ON al	.artist_id = ar.artist_id
)
SELECT 
    customer_name,
    artist_name,
    SUM(amount_spent) AS total_spent
FROM cte_customer_artist_spend
GROUP BY customer_name, artist_name
ORDER BY customer_name, total_spent DESC;
```


13. **We want to find out the most popular music Genre for each country. We determine the most popular genre as the genre with the highest amount of purchases. Write a query that returns each country along with the top Genre. For countries where the maximum number of purchases is shared return all Genres.**

```sql
WITH genre_sales AS (
    SELECT 
        c.country, g.name AS genre_name,
        COUNT(il.invoice_line_id) AS purchase_count,
        RANK() OVER (PARTITION BY c.country 
            ORDER BY COUNT(il.invoice_line_id) DESC) AS rnk
    FROM customer c
INNER JOIN invoice i ON c.customer_id = i.customer_id
INNER JOIN invoice_line il ON i.invoice_id = il.invoice_id
INNER JOIN track t ON il.track_id = t.track_id
INNER JOIN genre g ON t.genre_id = g.genre_id
    GROUP BY c.country, g.name
)
SELECT country, genre_name	
FROM genre_sales
WHERE rnk = 1;
```


## 📌 Key Findings

### 1. Senior-most Employee
- **General Manager** is the senior-most position.

### 2. Top 3 Countries with Most Invoices
- **USA, Canada, and Brazil** generate the highest number of invoices.

### 3. Highest Revenue by Media Type
- **MPEG Audio File (.mp3)** contributes the maximum revenue.

### 4. Best Customer City
- **Paris** is the city with maximum revenue → prime target for promotions / festivals.

### 5. Rock Listeners Profile
- Rock listeners exist across multiple countries — useful for targeted campaigns.
