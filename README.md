# 🎵 Digital Music Store SQL Project

## Project Overview

**Project Title**: Music Store SQL Analysis              
**Database**: `Music_Store_Database.sql`

This project contains a set of SQL queries designed to analyze a Music Store Database.
The queries cover business questions such as customer behavior, revenue insights, and artist popularity.

## 📂 Project Structure

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


## 📝 Sample Queries
1. **Most Senior Employee**

```sql
SELECT * FROM employee
ORDER BY levels DESC
LIMIT 1
```


2. **Top 3 Countries with Most Invoices**

```sql
SELECT billing_country, count(total) 
AS total_invoices FROM invoice
GROUP BY billing_country 
ORDER BY total_invoices DESC
LIMIT 3;
```

3. **Revenue by Media Type**

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
Note: Full queries are available in music_store_analysis.sql

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

## 💡 Business Impact

This SQL analysis can help the store:

- Optimize marketing campaigns by country & genre
- Identify high-value customers and cities for promotion
- Track artist & album performance to manage inventory & pricing
