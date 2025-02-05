# Comprehensive-Analysis-of-Zomato-s-Restaurant-and-Customer-Data-to-Drive-Business-Growth

This project focuses on analyzing Zomato's restaurant and customer data to extract actionable insights that can help improve operational efficiency, enhance customer experience, and drive revenue growth.

The dataset includes information about orders, customers, restaurants, ratings, cuisines, delivery times, referrals, and more. By leveraging SQL queries, statistical analysis, and visualization tools, the project addresses key business questions such as identifying top-performing restaurants, understanding customer behavior, optimizing delivery times, and improving referral programs.

The ultimate goal is to provide stakeholders with data-driven recommendations to make informed decisions and implement strategies that align with Zomato's business objectives.

#  Objectives of the Project

- Understand Customer Behavior : Analyze ordering patterns, preferences, and engagement metrics to identify loyal customers and high-value users.
- Evaluate Restaurant Performance : Assess revenue generation, average order values, delivery times, and customer ratings to rank and categorize restaurants.
- Optimize Referral Programs : Identify influential referrers and understand the impact of referrals on user acquisition.
- Improve Operational Efficiency : Optimize delivery times, categorize restaurants based on revenue performance, and suggest improvements in resource allocation.
- Enhance Marketing Strategies : Identify trends in cuisine preferences, premium customer segments, and flash sale impacts to guide targeted marketing campaigns.
- Provide Actionable Recommendations : Translate insights into clear, actionable steps for stakeholders to improve customer retention, increase revenue, and enhance overall service 
  quality.

# Business Questions Addressed


The project addresses the following key business questions:

How We Solved the Business Questions

- **Write an SQL query to find the top 5 most-ordered dishes from a given restaurant.**

      SELECT TOP 5 DishName, SUM(Quantity) AS TotalOrders
      FROM Orders
      WHERE RestaurantID = 'R101'
      GROUP BY DishName
      ORDER BY TotalOrders DESC;
  
- **Write a Query to Retrieve All Orders Placed in the Last 30 Days for a Specific User.**

      SELECT *
      FROM UserOrders
      WHERE UserID = 'U123' AND OrderDate >= DATEADD(DAY, -30, GETDATE());
  
- **Write a Query to Retrieve All Orders Placed in the Last 30 Days for a Specific User.**

      SELECT SUM(Amount) AS TotalRevenue
      FROM RestaurantRevenue
      WHERE OrderDate >= DATEADD(MONTH, -1, GETDATE());

- **Write a Query to Retrieve the Top 10 Restaurants with the Highest Average Customer Rating.**

      SELECT TOP 10 RestaurantID, AVG(Rating) AS AverageRating
      FROM Ratings
      GROUP BY RestaurantID
      ORDER BY AverageRating DESC;

- **Write a Query to List All Customers Who Have Ordered at Least One Dish from More Than 3 Different Restaurants..**

      SELECT UserID
      FROM Orders
      GROUP BY UserID
      HAVING COUNT(DISTINCT RestaurantID) > 3;

- **Write a Query to Identify Restaurants Where the Average Order Value Is Higher Than the Overall Average Order Value Across All Restaurants.**
 
      WITH AvgOrderValue AS (
      SELECT RestaurantID, AVG(Amount) AS AvgValue
      FROM RestaurantRevenue
      GROUP BY RestaurantID
      ),
      OverallAvg AS (
      SELECT AVG(Amount) AS OverallAverage
      FROM RestaurantRevenue
      )
      SELECT RestaurantID, AvgValue
      FROM AvgOrderValue
      WHERE AvgValue > (SELECT OverallAverage FROM OverallAvg);

 - **Write a Query to Group Orders by Cuisine Type and Calculate the Total Revenue for Each Cuisine.**

       SELECT Cuisine, SUM(Amount) AS TotalRevenue
       FROM CuisineOrders
       GROUP BY Cuisine;

- ** Write a Query to Find the Rank of Each Restaurant Based on Total Revenue Within Each City.**
 
        SELECT RestaurantID, City, TotalRevenue, RANK() OVER (PARTITION BY City ORDER BY TotalRevenue DESC) AS Rank
        FROM (
        SELECT RestaurantID, City, SUM(Amount) AS TotalRevenue
        FROM CityOrders
        GROUP BY RestaurantID, City
        ) AS RevenueByCity;
    
 - ** Categorize Restaurants into "High Revenue," "Medium Revenue," and "Low Revenue" Based on Their Monthly Sales.**

       SELECT RestaurantID, SUM(Amount) AS TotalMonthlyRevenue,
       CASE
           WHEN SUM(Amount) > 300 THEN 'High Revenue'
           WHEN SUM(Amount) BETWEEN 150 AND 300 THEN 'Medium Revenue'
           ELSE 'Low Revenue'
           END AS RevenueCategory
       FROM RestaurantRevenue
       WHERE MONTH(OrderDate) = MONTH(GETDATE())
       GROUP BY RestaurantID;

- ** Write a Query to Find the Top 3 Dishes Sold for Each Restaurant in a Specific City.**

      WITH RankedDishes AS (
      SELECT RestaurantID, City, DishName, SUM(Quantity) AS TotalQuantity,
           RANK() OVER (PARTITION BY RestaurantID ORDER BY SUM(Quantity) DESC) AS Rank
      FROM Orders
      WHERE City = 'New York'
      GROUP BY RestaurantID, City, DishName
      )
      SELECT RestaurantID, City, DishName, TotalQuantity
      FROM RankedDishes
      WHERE Rank <= 3;

  - ** Use a CTE to Calculate the Monthly Active Users and Their Most Ordered Dish for the Past 6 Months.**
 
        WITH RecentOrders AS (
        SELECT UserID, DishName, FORMAT(OrderDate, 'yyyy-MM') AS Month, COUNT(*) AS OrderCount
        FROM Orders
        WHERE OrderDate >= DATEADD(MONTH, -6, GETDATE())
        GROUP BY UserID, DishName, FORMAT(OrderDate, 'yyyy-MM')
        ),
        MostOrderedDish AS (
        SELECT UserID, Month, DishName, OrderCount,
           RANK() OVER (PARTITION BY UserID, Month ORDER BY OrderCount DESC) AS Rank
        FROM RecentOrders
        )
        SELECT UserID, Month, DishName AS MostOrderedDish
        FROM MostOrderedDish
        WHERE Rank = 1;

    - **Write a Query to Find All Users Who Have Referred Others (Directly or Indirectly) to Zomato's Referral Program.**
   
          WITH ReferrerHierarchy AS (
          SELECT ReferrerID, ReferredID
          FROM Referrals
          UNION ALL
          SELECT r.ReferrerID, rf.ReferredID
          FROM Referrals r
          INNER JOIN ReferrerHierarchy rf ON r.ReferredID = rf.ReferrerID
           )
          SELECT DISTINCT ReferrerID
          FROM ReferrerHierarchy;
   
    - **Write a Query to Retrieve the Top 5 Restaurants With the Fastest Average Delivery Time.**
   
          SELECT TOP 5 RestaurantID, AVG(DeliveryTime) AS AvgDeliveryTime
          FROM DeliveryTimes
          GROUP BY RestaurantID
          ORDER BY AvgDeliveryTime ASC;
    
# Extracted Insights

- **Customer Behavior :**
  
  - Identified loyal customers who frequently place orders and contribute significantly to revenue.
  - Found that certain users have not placed orders in the last 6 months, indicating potential churn risks.

- **Restaurant Performance :**

  - Top-performing restaurants were identified based on revenue, ratings, and delivery times.
  - Restaurants with low average ratings were flagged for improvement in service quality.

- **Referral Program Impact :**
  
  - Direct and indirect referrers were identified, highlighting the effectiveness of the referral program.
  - Recommendations were made to incentivize top referrers further.

- **Operational Efficiency :**
  
  - Restaurants with slow delivery times were identified, suggesting the need for better logistics management.
  - Flash sales significantly boosted orders for certain dishes, providing insights into customer preferences.

- **Marketing Opportunities :**
  
  - Chinese cuisine generated the highest revenue, followed by Italian and Indian cuisines.
  - Premium customers were concentrated in specific cities, offering opportunities for targeted marketing campaigns.

# Recommendations to Stakeholders

- **Customer Retention :**
  
  - Launch re-engagement campaigns for inactive users, offering discounts or personalized promotions.
  - Introduce loyalty programs to reward frequent customers.

- **Restaurant Support :**
  
  - Provide training and resources to restaurants with low ratings to improve service quality.
  - Collaborate with high-revenue restaurants to offer exclusive deals and promotions.

- **Referral Program Optimization :**
  
  - Increase referral rewards for top referrers to encourage more participation.
  - Use social media and email marketing to promote the referral program.

- **Operational Improvements :**
  
  - Invest in logistics infrastructure to reduce delivery times for underperforming restaurants.
  - Monitor delivery times regularly to ensure consistency.

- **Targeted Marketing :**
  
  - Focus on promoting high-revenue cuisines in regions where they are popular.
  - Design flash sales and limited-time offers for dishes with high demand.

- **Premium Customer Engagement :**
  
  - Offer exclusive benefits to premium customers, such as early access to new features or personalized discounts.
  - Expand marketing efforts in cities with a high concentration of premium customers.
