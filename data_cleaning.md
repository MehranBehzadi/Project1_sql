-- Fill missing values in sentimentScore and sentimentMagnitude with 0
UPDATE products
SET sentimentScore = COALESCE(sentimentScore, 0),
    sentimentMagnitude = COALESCE(sentimentMagnitude, 0);




-- Standardize product names to title case
UPDATE products
SET name = INITCAP(name);


-- Flag outliers based on arbitrary thresholds (adjust as necessary)
SELECT SKU, name, orderedQuantity, stockLevel
FROM products
WHERE orderedQuantity > 1000 OR stockLevel > 1000
   OR orderedQuantity < 0 OR stockLevel < 0;



-- Add constraints to ensure stockLevel and orderedQuantity are non-negative
ALTER TABLE products
ADD CONSTRAINT positive_orderedQuantity CHECK (orderedQuantity >= 0),
ADD CONSTRAINT positive_stockLevel CHECK (stockLevel >= 0);


-- Set NULL values in stockLevel and orderedQuantity to 0 before adding NOT NULL constraints
UPDATE products
SET stockLevel = COALESCE(stockLevel, 0),
    orderedQuantity = COALESCE(orderedQuantity, 0);

-- Then, add the NOT NULL constraints
ALTER TABLE products
ALTER COLUMN stockLevel SET NOT NULL,
ALTER COLUMN orderedQuantity SET NOT NULL;


--Confirm the changes made to the table
SELECT column_name, data_type, is_nullable
FROM information_schema.columns
WHERE table_name = 'products';


I went over products table to fix and clean the data

