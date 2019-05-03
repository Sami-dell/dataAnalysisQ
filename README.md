## Q1

### Use the Invoice table to determine the countries that have the most invoices. Provide a table of BillingCountry and Invoices ordered by the number of invoices for each country. The country with the most invoices should appear first.


select
BillingCountry, count(*)
from Invoice
group by billingcountry 
order by count(*) desc

## Q2

### We would like to throw a promotional Music Festival in the city we made the most money. Write a query that returns the 1 city that has the highest sum of invoice totals. Return both the city name and the sum of all invoice totals.

select billingcity,
sum(total)
from invoice
group by billingcity 
order by total desc
limit 1

## Q3

### The customer who has spent the most money will be declared the best customer. Build a query that returns the person who has spent the most money. I found the solution by linking the following three: Invoice, InvoiceLine, and Customer tables to retrieve this information, but you can probably do it with fewer!

select c.customerid, c.firstname, c.lastname,
sum(total) as invoices
from invoice i
join customer c
on i.customerid = c.customerid 
group by c.customerid 
order by invoices desc
limit 1

## Q4

### The team at Chinook would like to identify all the customers who listen to Rock music. Write a query to return the email, first name, last name, and Genre of all Rock Music listeners. Return your list ordered alphabetically by email address starting with 'A'.

select c.email, c.firstname, c.lastname, g.name
from customer c
join invoice i
on c.customerid = i.customerid
join invoiceline il
on i.invoiceid = il.invoiceid
join track t
on t.trackid = il.trackid
join genre g
on g.genreid = t.genreid
where g.name = "Rock"
group by email
order by c.email asc


## Q5

### Write a query that determines the customer that has spent the most on music for each country. Write a query that returns the country along with the top customer and how much they spent. For countries where the top amount spent is shared, provide all customers who spent this amount.

### You should only need to use the Customer and Invoice tables.

with t1 as (select c.customerId Customerid, c.firstname FirstName, c.lastname LastName, c.country Country , sum(i.total) as TotalSpent
from customer c
join invoice i
on c.customerid = i.customerid
group by c.customerid),

t2 as (select customerid, firstname, lastname, country, max(TotalSpent) max
from t1 
group by country
order by country
)
select t1.customerid, t1.firstname, t1.lastname, t1.country, t1.totalspent
from t1
join t2
on t1.country = t2.country
where t1.totalspent = t2.max
order by t1.country
