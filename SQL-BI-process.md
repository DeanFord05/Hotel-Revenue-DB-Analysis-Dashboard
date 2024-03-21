<h1><b>MS SQL Server:</b></h1>

# Create database in MS SQL Server and upload MS Excel sheet.

# Combining all sale years into one table for Exploratory Data Analysis
    with hotels as (
    select * from dbo.['2018$']
    union
    select * from dbo.['2019$']
    union
    select * from dbo.['2020$']);

<h1><b>Questions To Answer:</b></h1>

# Do the Hotels gain Revenue Every Year? 
    select arrival_date_year, ROUND(SUM((stays_in_week_nights+stays_in_weekend_nights)*adr), 2) as revenue 
    from hotels
    group by arrival_date_year;

--> up ~16 million USD From 2018-2019 but down ~6 million USD from 2019-2020, however this is due to the 2020 year not fully being finished

# Which hotel type is more profitable?
    select hotel, ROUND(SUM((stays_in_week_nights+stays_in_weekend_nights)*adr), 2) as revenue 
    from hotels
    group by hotel;

--> City Hotels with ~2 million more in total revenue

# What months had the most customers?
    select arrival_date_month, (SUM(stays_in_week_nights)+SUM(stays_in_weekend_nights)) as total_nights 
    from hotels 
    group by arrival_date_month 
    order by total_nights DESC;

--> August had the most amount of total customers with 57.5k. July being the second most with 50k.

# What were the top 5 countrys in terms of revenue?
    select top 5 country, ROUND(SUM((stays_in_week_nights+stays_in_weekend_nights)*adr),2) as revenue
    from hotels
    group by country
    order by revenue desc;

--> Portugal(PRT) was the most profitable country with $11m in revenue followed by Great Britain(GBR), France(FRA), Spain(ESP) and Germany(DEU).

# What room type was the most popular and what was its revenue?
    select assigned_room_type as room_type, SUM(stays_in_week_nights+stays_in_weekend_nights) as total_nights, ROUND(SUM((stays_in_week_nights+stays_in_weekend_nights)*adr),2) as revenue
    from hotels
    group by assigned_room_type
    order by total_nights desc

--> Room type A was the most popular and also had the highest revenue with 185k total nights and $17.5m in revenue

<h1><b>Preping for dashboarding:</b></h1>

# combining all tables for dashboarding
    with hotels as (
    select * from dbo.['2018$']
    union
    select * from dbo.['2019$']
    union
    select * from dbo.['2020$'])

    select * from hotels
    left join dbo.market_segment$
    on hotels.market_segment = market_segment$.market_segment
    left join dbo.meal_cost$
    on meal_cost$.meal = hotels.meal;

<h1><b>Power BI:</b></h1>

# import SQL Server with sql query above^

# create new column named Revenue
    Formula: =([stays_in_week_nights]+[stays_in_weekend_nights])*([adr]-([adr]*[Discount]))

# Total Nights Visual
    Formula: =(SUM([stays_in_week_nights])+SUM([stays_in_weekend_nights]))
