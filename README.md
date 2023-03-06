# Case study : Data Mart Analysis using Sql 
Data mart is a latest  shopping venture which has (shopify)online site and retail platforms options & i have analysed sales and performance of this venture since large scale supply changes were made at Data Mart. All products now use sustainable packaging methods in every single step from the farm all the way to the customer.
This data has around 17,000 records.
# Created a datamart schema and inserted all the data records <img width="345" alt="Screenshot 2023-03-06 133409" src="https://user-images.githubusercontent.com/100594641/223052217-7bd3dac3-a6c5-4892-8b0a-637404f0f8c4.png"> <img width="620" alt="Screenshot 2023-03-06 133546" src="https://user-images.githubusercontent.com/100594641/223052456-9825943b-f68b-4acd-a6c6-b853b6162ad2.png"> 
all the way until 17000 records

the weekly_sales table has been created  
<img width="402" alt="Screenshot 2023-03-06 133854" src="https://user-images.githubusercontent.com/100594641/223053082-ded9ba6e-90ff-422a-8efd-3b5dfa79572f.png">
# Performing Data cleansing on weekly_sales table itself and stored the clean data got from weekly_sales into a new table called clean_weekly_sales. 
Added a week_number, month_number, calendar_year etc , separated customers according to age_band & various other cleaning steps in order to get meaningful insights
## Data Cleansing
CREATE TABLE clean_weekly_sales AS
SELECT
week_date,
  week(week_date) AS week_number,
  month(week_date) AS month_number,
  year(week_date) AS calendar_year,
  region,
  platform,
  CASE
    WHEN segment = 'null' THEN 'Unknown'
    ELSE segment
    END AS segment,
  CASE
    WHEN right(segment, 1) = '1' THEN 'Young Adults'
    WHEN right(segment, 1) = '2' THEN 'Middle Aged'
    WHEN right(segment, 1) IN ('3', '4') THEN 'Retirees'
    ELSE 'Unknown'
    END AS age_band,
  CASE
    WHEN left(segment, 1) = 'C' THEN 'Couples'
    WHEN left(segment, 1) = 'F' THEN 'Families'
    ELSE 'Unknown'
    END AS demographic,
  customer_type,
  transactions,
  sales,
  ROUND(
      sales / transactions,
      2
   ) AS avg_transaction
   FROM weekly_sales;
   
   <img width="586" alt="Screenshot 2023-03-06 135914" src="https://user-images.githubusercontent.com/100594641/223057081-95ed444f-60b6-4e5d-91df-22bf7a301ae0.png"> <img width="173" alt="Screenshot 2023-03-06 140319" src="https://user-images.githubusercontent.com/100594641/223058225-6a98ff45-dd7e-4da9-8993-bd723e8b06a0.png">
# Data Exploration
i just retrieved missing week numbers from the data

<img width="629" alt="Screenshot 2023-03-06 222316" src="https://user-images.githubusercontent.com/100594641/223177969-61e4f5ec-6845-4f67-90de-f4b1558a6668.png">

Total transactions in each year

<img width="314" alt="Screenshot 2023-03-06 222925" src="https://user-images.githubusercontent.com/100594641/223179056-0288ce75-56d6-41ea-8417-7b8b8cb7265c.png">

Total sales for each region for each month

<img width="363" alt="Screenshot 2023-03-06 223351" src="https://user-images.githubusercontent.com/100594641/223180097-21c9deee-3a36-4be2-9619-6f85fc5569c6.png">

total count of transactions for each platform

<img width="290" alt="Screenshot 2023-03-06 223645" src="https://user-images.githubusercontent.com/100594641/223180733-f0ae550c-b051-4641-a4af-7a624b12ab49.png">

percentage of sales for Retail vs Shopify for each month

WITH cte_monthly_platform_sales AS (
  SELECT
    month_number,calendar_year,
    platform,
    SUM(sales) AS monthly_sales
  FROM clean_weekly_sales
  GROUP BY month_number,calendar_year, platform
)
SELECT
  month_number,calendar_year,
  ROUND(
    100 * MAX(CASE WHEN platform = 'Retail' THEN monthly_sales ELSE NULL END) /
      SUM(monthly_sales),
    2
  ) AS retail_percentage,
  ROUND(
    100 * MAX(CASE WHEN platform = 'Shopify' THEN monthly_sales ELSE NULL END) /
      SUM(monthly_sales),
    2
  ) AS shopify_percentage
FROM cte_monthly_platform_sales
GROUP BY month_number,calendar_year
ORDER BY month_number,calendar_year;

<img width="487" alt="Screenshot 2023-03-06 224132" src="https://user-images.githubusercontent.com/100594641/223181827-e6b519c9-3044-4868-9c99-3d1406a30ac3.png">

percentage of sales by demographic for each year in the dataset

<img width="544" alt="Screenshot 2023-03-06 224427" src="https://user-images.githubusercontent.com/100594641/223182509-63305cb0-42d2-46f5-9346-eb955526dbc7.png">


which age_band and demographic values contribute the most to Retail sales

<img width="528" alt="Screenshot 2023-03-06 224627" src="https://user-images.githubusercontent.com/100594641/223182972-1ff01ce4-af35-4d61-bbd3-90accca8ba55.png">

