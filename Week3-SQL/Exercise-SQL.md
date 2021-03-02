
# SQL:  Structured Query Language  Exercise

### Getting Started
1. Go to BigQuery UI https://console.cloud.google.com/bigquery
2. Add in the public data sets. 
	3. Click the Add Data icon
	4. Add any dataset
	5. `bigquery-public-data` should become visible and populate in the BigQuery UI. 
3. Add your queries where it says [YOUR QUERY HERE].
4. Make sure you add your query in between the triple tick marks. 
---

For this section of the exercise we will be using the `bigquery-public-data.austin_311.311_service_requests`  table. 

5. Write a query that tells us how many rows are in the table. 
	```
	SELECT COUNT(*) FROM `bigquery-public-data.austin_311.311_service_requests`
    
    # 1,039,237 rows in table.
        ```

7. Write a query that tells us how many _distinct_ values there are in the complaint_description column.
	``` 
	SELECT COUNT(DISTINCT(complaint_description)) FROM `bigquery-public-data.austin_311.311_service_requests`
    
    # 158 distinct values in complaint_description column.
	```
  
8. Write a query that counts how many times each owning_department appears in the table and orders them from highest to lowest. 
	``` 
	SELECT owning_department, COUNT(*) as Amount FROM `bigquery-public-data.austin_311.311_service_requests`
    GROUP BY owning_department 
    ORDER BY Amount DESC
	```

9. Write a query that lists the top 5 complaint_description that appear most and the amount of times they appear in this table. (hint... limit)
	```
	SELECT complaint_description, COUNT(*) as Amount FROM `bigquery-public-data.austin_311.311_service_requests`
    GROUP BY complaint_description
    ORDER BY Amount DESC
    LIMIT 5
    
    # Austin Code - Request Code Officer
    # Traffic Signal - Maintenance
    # Loose Dog
    # ARR Missed Recycling
    # Animal Control - Assistance Request
	  ```
10. Write a query that lists and counts all the complaint_description, just for the where the owning_department is 'Animal Services Office'.
	```
	SELECT complaint_description, COUNT(*) as Amount FROM `bigquery-public-data.austin_311.311_service_requests`
    WHERE owning_department = 'Animal Services Office'
    GROUP BY complaint_description
    ORDER BY Amount DESC
	```

11. Write a query to check if there are any duplicate values in the unique_key column (hint.. There are two was to do this, one is to use a temporary table for the groupby, then filter for values that have more than one count, or, using just one table but including the  `having` function). 
	```
	WITH G AS (
        SELECT unique_key, COUNT(*) as Amount 
        FROM `bigquery-public-data.austin_311.311_service_requests`
        GROUP BY unique_key
    )

    SELECT * FROM G WHERE Amount > 1
    
    # Returns no results, so no duplicates
	```


### For the next question, use the `census_bureau_usa` tables.

1. Write a query that returns each zipcode and their population for 2000 and 2010. 
	```
	WITH A AS (
    SELECT 
        zipcode, population
    FROM 
        `bigquery-public-data.census_bureau_usa.population_by_zip_2010`
    )

    , B AS (
            SELECT 
                zipcode, population
            FROM 
                `bigquery-public-data.census_bureau_usa.population_by_zip_2000`
    )

    SELECT A.zipcode, A.population as population_2010, B.population as population_2000 FROM A JOIN B ON A.zipcode = B.zipcode
	```

### For the next section, use the  `bigquery-public-data.google_political_ads.advertiser_weekly_spend` table.
1. Using the `advertiser_weekly_spend` table, write a query that finds the advertiser_name that spent the most in usd. 
	```
    SELECT advertiser_name, SUM(spend_usd) as Amount_USD FROM `bigquery-public-data.google_political_ads.advertiser_weekly_spend`
    GROUP BY advertiser_name, spend_usd
    ORDER BY Amount_USD DESC
	```
2. Who was the 6th highest spender? (No need to insert query here, just type in the answer.)
	```
	Senate Leadership Fund
	```

3. What week_start_date had the highest spend? (No need to insert query here, just type in the answer.)
	```
	2020-10-18
	```

4. Using the `advertiser_weekly_spend` table, write a query that returns the sum of spend by week (using week_start_date) in usd for the month of August only. 
	```
	SELECT week_start_date, SUM(spend_usd) as spend_usd_aug FROM `bigquery-public-data.google_political_ads.advertiser_weekly_spend`
    WHERE CAST(week_start_date AS string) LIKE '%-08-%'
    GROUP BY week_start_date
	```
6.  How many ads did the 'TOM STEYER 2020' campaign run? (No need to insert query here, just type in the answer.)
	```
	50 ads.
	```
7. Write a query that has, in the US region only, the total spend in usd for each advertiser_name and how many ads they ran. (Hint, you're going to have to join tables for this one). 
	```
		[YOUR QUERY HERE] - Not sure how to proceed with this one (extracting US region ads).
	```
8. For each advertiser_name, find the average spend per ad. 
	```
	SELECT advertiser_name, 
       AVG(spend_usd) as s_usd,
       AVG(spend_eur) as s_eur,
       AVG(spend_inr) as s_inr,
       AVG(spend_bgn) as s_bgn, 
       AVG(spend_hrk) as s_hrk, 
       AVG(spend_czk) as s_czk,
       AVG(spend_dkk) as s_dkk,
       AVG(spend_huf) as s_huf,
       AVG(spend_pln) as s_pln,
       AVG(spend_ron) as s_ron,
       AVG(spend_sek) as s_sek,
       AVG(spend_gbp) as s_gbp,
       AVG(spend_nzd) as s_nzd
        
    FROM `bigquery-public-data.google_political_ads.advertiser_weekly_spend`
    GROUP BY advertiser_name
	```
10. Which advertiser_name had the lowest average spend per ad that was at least above 0. 
	``` 
	WITH T AS (
       SELECT advertiser_name, AVG(spend_usd) as aver
       FROM `bigquery-public-data.google_political_ads.advertiser_weekly_spend`
       GROUP BY advertiser_name
       ORDER BY aver ASC
    )

    SELECT * FROM T
    WHERE aver > 0
    ORDER BY aver
    LIMIT 1
    
    # Brandy K. Chambers
	```
## For this next section, use the `new_york_citibike` datasets.

1. Who went on more bike trips, Males or Females?
	```
	SELECT gender, COUNT(gender) FROM `bigquery-public-data.new_york.citibike_trips`
    GROUP BY gender
    
    # Males went on more bike trips
	```
2. What was the average, shortest, and longest bike trip taken in minutes?
	```
	[YOUR QUERY HERE]
    
    Longest - 6707533
    
    SELECT * FROM `bigquery-public-data.new_york.citibike_trips`
    ORDER BY tripduration DESC
    LIMIT 1
    
    Shortest - 60
    
    SELECT * FROM `bigquery-public-data.new_york.citibike_trips`
    ORDER BY tripduration ASC
    LIMIT 1
    
    Avg - 933.7485653764315
    
    SELECT AVG(tripduration) FROM `bigquery-public-data.new_york.citibike_trips`
    
	```

3. Write a query that, for every station_name, has the amount of trips that started there and the amount of trips that ended there. (Hint, use two temporary tables, one that counts the amount of starts, the other that counts the number of ends, and then join the two.) 
	```
	WITH A AS (
    SELECT 
        start_station_name, COUNT(start_station_name) as start_count 
    FROM `bigquery-public-data.new_york.citibike_trips`
    GROUP BY start_station_name
    )
    , B AS (

        SELECT 
            end_station_name, COUNT(end_station_name) as end_count 
        FROM `bigquery-public-data.new_york.citibike_trips`
        GROUP BY end_station_name
    )

    SELECT A.start_station_name, A.start_count, B.end_count FROM A JOIN B ON A.start_station_name = B.end_station_name
	```
# The next section is the Google Colab section.  
1. Open up this [this Colab notebook](https://colab.research.google.com/drive/1kHdTtuHTPEaMH32GotVum41YVdeyzQ74?usp=sharing).
2. Save a copy of it in your drive. 
3. Rename your saved version with your initials. 
4. Click the 'Share' button on the top right.  
5. Change the permissions so anyone with link can view. 
6. Copy the link and paste it right below this line. 
	* YOUR LINK: https://colab.research.google.com/drive/1UfGbF7HxMsWA5E0cnrcvh1mLKueGD-Ks?usp=sharing
9. Complete the two questions in the colab notebook file. 
