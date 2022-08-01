#data_cleaning #mysql 

  5 tips you can use to get your data ready...  
  
  
#  Find "holes" in your data:  
```sql
# missing data in a particular column  

WHERE column_a IS NULL  
  
# patterns associated with holes  

SELECT  
column_b,  
COUNT(*) AS records  
FROM tablename  
WHERE column_a IS NULL  
GROUP BY column_b  
ORDER BY COUNT(*) DESC;
```  
# Flag certain records (simple binary column):  
```SQL
CASE  
WHEN column_a is NULL  
OR column_b = 'some impossible value'  
THEN 1  
ELSE 0  
END AS is_dirty  
```  

  
  
# Clean up messy values (to standardize them):  
```SQL
CASE  
WHEN location IN('CA','ca','San Diego, CA') THEN 'CA'  
ELSE location  
END AS clean_location  
```  

  
  
# Bucket overly granular values into segments:  
```SQL
CASE  
WHEN age < 18 THEN '1. < 18'  
WHEN age BETWEEN 18 AND 35 THEN '2. 18-35'  
WHEN age > 35 THEN '3. > 35'  
END AS age_group  
``` 

  
  
#  Analyze values across multiple records (powerful):  
```SQL
-- note the use of 3,2,1 to reorder the values  
  
SELECT  
subscriber_id,  
MAX(  
CASE  
WHEN status = 'paying_subscriber' THEN '3. Active'  
WHEN status = 'still_in_trial' THEN '2. Trial'  
WHEN status = 'canceled' THEN '1. Canceled'  
END) AS current_status  
FROM subscription_table  
GROUP BY subscriber_id
``` 

