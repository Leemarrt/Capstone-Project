# Restaurant Market Analysis & Investment Opportunities in Mexico

A Power BI Capstone Project exploring demand–supply gaps, consumer preferences, and restaurant performance to guide strategic investment.
[Dashboard Preview](./screenshots/dashboard.png)

## Project Overview
This project analyzes the **Mexican restaurant market** using survey data on restaurants, consumers, cuisines, and ratings.  
The goal is to uncover **where demand outpaces supply**, identify **consumer segments and preferences**, and provide **evidence-based recommendations** for investors and entrepreneurs.

Key features:
- Data cleaning & modeling in **Power Query**  
- Star-schema design with **fact and dimension tables**  
- Advanced **DAX measures** for demand vs. supply analysis  
- Interactive **Power BI dashboard** with slicers, KPIs, and visuals  

## Executive Summary
The restaurant market shows an **even city distribution**, with **San Luis Potosí** leading in concentration.  
**Students** form the largest demographic segment, typically **single, independent, and child-free**—shaping price sensitivity and preferences.  

The **top-rated restaurant (ID 132955)** combines **low pricing** with **wine & beer service** and **smoking allowed**, suggesting these factors influence consumer preference.  
The **Mexican & Regional cuisine group** shows the **largest unmet demand** (gap score ≈ **0.89**), highlighting a strong growth opportunity.  

**Recommendations**:
1. Expand capacity in **Mexican & Regional cuisines**.  
2. Target **student-dense cities** such as San Luis Potosí.  
3. Optimize for **mid/low pricing** and consider **alcohol/smoking policies** per local norms.  

Full details available in [Executive_Summary.md](./Executive_Summary.md).
## Business Questions (Q1–Q4)
1. **Market Overview** – Which cities have the highest restaurant presence and ratings?  
2. **Consumer Profile** – What are the consumer demographics and cuisine preferences?  
3. **Market Gaps** – Which cuisine groups are underserved (Demand > Supply)?  
4. **Investment Lens** – Which characteristics (price, franchise, policies) should investors back?  

## Data Sources
- **restaurants.csv** – restaurant attributes (City, State, Price, Alcohol_Service, Smoking_Allowed, etc.)  
- **ratings.csv** – ratings for Food, Service, and Overall  
- **restaurant_cuisines.csv** – bridge table linking restaurants and cuisines  
- **consumers.csv** – demographics (Age, Budget, Occupation, etc.)  
- **consumers_preferences.csv** – preferred cuisines per consumer  


## Data Preparation
- Cleaned text fields: trimmed, proper-cased, replaced blanks with `"Unknown"`.  
- Standardized **Restaurant_ID** and **Consumer_ID** as text keys.  
- Normalized Smoking/Franchise to **Yes/No** only.  
- Backfilled missing **City** values using **State**.  
- Created **Age Groups** (Children, Early Working, Prime, Mature, Elderly).  
- Built **Cuisine Groups & Sort Keys** for consistent visuals.
- Created **dim_cuisines.csv** – canonical cuisine list & groups

## Data Model (Star Schema)
restaurants (1) ──► (*) restaurant_cuisines ◄── (1) dim_cuisines
restaurants (1) ──► (*) ratings ◄── (1) consumers
consumers   (1) ──► (*) consumer_preferences ◄── (1) dim_cuisines

> Cross-filter: restaurants → restaurant_cuisines set to **Both** (to allow cuisine slicing).

##  Limitations & Assumptions
- **Single-country scope:** The dataset covers only Mexican restaurants, so results are not globally generalizable.  
- **Survey-based data:** Ratings and consumer preferences are self-reported and may include bias.  
- **City data quality:** Some records required filling missing city values with state or “Unknown,” which may affect precision.  
- **Cuisine categorization:** Cuisines were manually grouped; overlap between groups (e.g., “Fast Food” vs “Burgers”) may influence counts.  
- **Static snapshot:** Data represents a snapshot in time, not longitudinal changes in the market.  


## Key DAX Measures
```DAX
Restaurants Count = DISTINCTCOUNT(restaurants[Restaurant_ID])
Avg Overall Rating = AVERAGE(ratings[Overall_Rating])

Franchise % =
DIVIDE(
    CALCULATE([Restaurants Count], restaurants[Franchise] = "Yes"),
    CALCULATE([Restaurants Count], REMOVEFILTERS(restaurants[Franchise])),
    0
)

Cuisine_Demand_Share =
DIVIDE([Cuisine_Demand], CALCULATE([Cuisine_Demand], REMOVEFILTERS(dim_cuisines)), 0)

Opportunity Score = [Cuisine_Demand_Share] - [Cuisine_Supply_Share].


 
