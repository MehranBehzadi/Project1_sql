QA step 1: validate check number of total transaction revenues that are not null with total transaction revenue for each city and country
using the query below it returns with 81 rows

SELECT case
	when city = 'not available in demo dataset' then 'unknown'
	ELSE city
	END,
 country, totaltransactionrevenue 
	from all_sessions
	WHERE totaltransactionrevenue is not null
	order by totaltransactionrevenue 

similarly, the following query returns 81 for number of rows for total transaction revenues that are not null

	SELECT count(totaltransactionrevenue) from all_sessions
	WHERE totaltransactionrevenue is not null


Given the dataset, it is difficult to join tables and find appropriate keys or a column values that match, which could result in errors.
However tables all_sessions, products, analytics have common column value types in which allow to join table and write queries. for example,
according to question 4, from part 2, I was able to join productsku from all_sessions table and sku from products table together and get the top selling product for each and country. I also made assumption where the question is asking about "top-selling product" for each city and country because there was no accurate revenue for products to measure by that criteria, I measured the top-selling product by quantity of orders for that products. Although this may not be accurate enough but given the available dataset, this is the most accurate way to measure the top-selling product.

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


For data cleaning part, I changed nulls and made sure columns have right data types, and some columns do not take negative values by adding constraints

the following query shows the data typesand nullability of the columns

SELECT column_name, data_type, is_nullable
FROM information_schema.columns
WHERE table_name = 'products';

