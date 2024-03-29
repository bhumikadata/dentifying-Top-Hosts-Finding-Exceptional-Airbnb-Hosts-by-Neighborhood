# Identifying-Top-Hosts-Finding-Exceptional-Airbnb-Hosts-by-Neighborhood

# Problem Statement

As a data analyst working for our travel company, we strive to recognize outstanding hosts within different neighborhoods based on their average ratings for listings. Our goal is to identify the top two hosts in each neighborhood who have the highest average ratings for their listings. This information will enable us to acknowledge exceptional hosts and potentially offer them incentives to maintain their excellent service.

![DAy 24](https://github.com/bhumikadata/dentifying-Top-Hosts-Finding-Exceptional-Airbnb-Hosts-by-Neighborhood/assets/131578649/140098c1-6971-4ea5-a7a7-30889ac56d17)


# Solution Overview

To address this challenge, I've developed a SQL query that extracts the top two hosts with the highest average ratings for their listings in each neighborhood. 
Here's how my solution works:

### Identify Hosts with More Than Two Listings: 
I started by creating a Common Table Expression (CTE) named more_than_2_listings to filter hosts who have at least two listings, as hosts with fewer listings may not be representative of their performance.

### Calculate Average Ratings: 
I joined the filtered host data with the reviews table to calculate the average ratings for each host.

### Rank Hosts by Average Ratings: 
Using the RANK() window function, I ranked hosts based on their average ratings in descending order.

### Select Top Two Hosts per Neighborhood: 
I selected the top two hosts with the highest average ratings in each neighborhood, considering only hosts with more than two listings.

## SQL Code 

``` sql 
-- Common Table Expression to identify hosts with more than two listings
WITH more_than_2_listings AS (
    SELECT  
        host_id,
        listing_id,
        COUNT(listing_id) OVER (PARTITION BY host_id) AS no_of_listings
    FROM 
        listings
),
-- CTE to calculate average ratings and rank hosts
ratings_rnk AS (
    SELECT 
        m.host_id,
        m.no_of_listings,
        AVG(r.rating) AS avg_rating,
        RANK() OVER (ORDER BY AVG(r.rating) DESC) AS rnk
    FROM 
        reviews r
    JOIN   
        more_than_2_listings m ON r.listing_id = m.listing_id 
    WHERE 
        no_of_listings >= 2 
    GROUP BY 
        m.host_id,
        m.no_of_listings
)
-- Select top two hosts with highest average ratings
SELECT 
    host_id,
    no_of_listings,
    avg_rating
FROM 
    ratings_rnk
WHERE 
    rnk <= 2;
```

# Potential Use Cases

### Host Recognition: 
Identifying top-performing hosts allows us to acknowledge their efforts and incentivize them to maintain high-quality service.

### Improving Customer Experience: 
Understanding which hosts consistently provide excellent experiences enables us to recommend their listings to customers, enhancing overall satisfaction.

### Business Strategy: 
Insights into host performance can inform business strategies, such as targeted marketing campaigns or partnership opportunities.


