Question 1: Which cities and countries have the highest level of transaction revenues on the site?

SELECT case
	when city = 'not available in demo dataset' then 'unknown'
	ELSE city
	END,
 country, totaltransactionrevenue 
	from all_sessions
	WHERE totaltransactionrevenue is not null
	order by totaltransactionrevenue 

	
Question 2: What is the average number of products ordered from visitors in each city and country?

SELECT case
	when city = '(not set)' then 'unknown'
	when city = 'not available in demo dataset' then 'unknown'
	ELSE city END, 
	case
	when country = '(not set)' then 'unknown'
	ELSE country END, 
	avg(cast(orderedquantity as int)) as avg_products_ordered
	FROM products 
	join all_sessions
	on all_sessions.productsku = products.sku
	WHERE orderedquantity is not null
	group by city, country
	order by avg_products_ordered DESC

Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?

select case
	when city = '(not set)' then 'unknown'
	when city = 'not available in demo dataset' then 'unknown'
	ELSE city END
	, case
	when country = '(not set)' then 'unknown'
	ELSE country END
	,v2productname, v2productcategory 
	from all_sessions
	order by city, country

Unfortunately there is no specific pattern to each city or country as products orders are quite vast and different. 
However, most of the orders are from Google and Youtube by United States; but products vary and they are different products
such as, apparels, drinkware, and electronics or accessories. Other countries such as Ukraine, Pakistan, Peru, and UK 
orders are very different and there is no pattern to them.

Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?

select case
	when city = '(not set)' then 'unknown'
	when city = 'not available in demo dataset' then 'unknown'
	ELSE city END,
 case
	when country = '(not set)' then 'unknown'
	ELSE country END,
	name,
 	sum(cast(orderedquantity as int)) as top_selling from products
	JOIN all_sessions on all_sessions.productsku=products.sku
	WHERE orderedquantity is not null
	group by city, country, name
	order by top_selling DESC	

Given the question is asking for "top-selling product" and the data do not provide enough information on the revenue of products,
my interpretation is to measure it by the number of products sold for each country and city. I also used the join method in the coding to 
add multiple columns from all_sessions and products table

Question 5: Can we summarize the impact of revenue generated from each city/country?


select case
	when city = '(not set)' then 'unknown'
	when city = 'not available in demo dataset' then 'unknown'
	ELSE city END,
 case
	when country = '(not set)' then 'unknown'
	ELSE country END,
	sum(cast(revenue as int)) as revenue from all_sessions
	JOIN analytics on analytics.visitid = all_sessions.visitid
	where revenue is not null
	group by city, country
	order by revenue DESC

From business standpoint, Revenue for each city and country demonstrates how well the business is doing in that area
This can determine the popularity of products and items being sold and the business can focus more on other cities and
countries to improve its revenue.


Question 6: categorize products based on their restock level needed for them. If there is no ordered quantity and no stock level mark them as 'discontinued'

select name, sku, stocklevel, orderedquantity,
	case WHEN cast(stocklevel as smallint)-cast(orderedquantity as smallint) > 100
	then 'No restock needed'
	WHEN cast(stocklevel as smallint)-cast(orderedquantity as smallint) = 0
	then 'discontinued'
	ELSE 'Need to restock'
	END as restock_level
	from products
	ORDER by sku

The logic behind this for me was to identify what products need to be restocked due to their high volume of orders so the business can keep up
with the products that need to be restocked before more orders coming in for them and make sure they are available. I made assumptions as well in this case, for example, products with no stock level and ordered quantity are discontinued as they serve no purpose.

Question 7: Count number of products ordered by each unique visitors for each city and country

select  DISTINCT(all_sessions.fullvisitorid), case
	when city = '(not set)' then 'unknown'
	when city = 'not available in demo dataset' then 'unknown'
	ELSE city END,
	case
	when country = '(not set)' then 'unknown'
	ELSE country END, 
	Count(*) as products_ordered
	from all_sessions
	JOIN analytics
	on all_sessions.visitid=analytics.visitid
	JOIN products
	on all_sessions.productsku=products.sku
	GROUP by all_sessions.fullvisitorid, city, country
	ORDER by country

I was trying to find how many products were ordered by each visitor for each city and country to demonstrate the interactions of the visitors
based on city and country. 

Question 8: find total orders bye each country

select case
	when country = '(not set)' then 'unknown'
	ELSE country END, 
	sum(cast(total_ordered as int)) total_orders_country from sales_report sr
JOIN all_sessions als
on als.productsku = sr.productsku
WHERE total_ordered != '0'
group by country
ORDER by total_orders_country DESC

Using all_sessions and sales_report tables, I wanted to know the total number of orders of products for each country and how well they are doing. 
From business perspective, this data helps to understand the total orders that are being trafficked to each country and how well they are doing.
I also made sure not to include if there is no total orders in any country
