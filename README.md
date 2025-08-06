# Wine Promotion Analysis

###  Project Overview
This project involves analyzing regional alcohol consumption patterns across Russia to identify the top 10 regions most similar to Saint Petersburg in their wine consumption habits.

A recent wine promotion campaign in Saint Petersburg was a massive success, and the company's marketing team is exploring the possibility of expanding the campaign to other regions. However, due to resource constraints, the promotion cannot be launched nationwide. Therefore, data-driven insights are needed to determine which regions are most likely to respond positively to a similar campaign.

### Data Sources
The primary dataset used in this project, russian_alcohol_consumption.csv, contains regional alcohol consumption data across various Russian territories over multiple years.

### Tools
- Microsoft Excel - Data Cleaning
- Microsoft Power BI - Data Visualization
- MySQL - Data Query & insights

### Exploratory Data Analysis
The Exploratory Data Analysis (EDA) phase played a crucial role in understanding the structure, distribution, and trends within the  dataset. It helped uncover key insights and patterns that guided the recommendation of regions for the wine promotion campaign. which helped us achieve the following:

- Understand the overall structure and cleanliness of the dataset.
- Measure alcohol consumption trends across regions and years.
- Identify consumption behavior specific to Saint Petersburg.
- Compare other regions to Saint Petersburg to find similarities in wine consumption.
- Support data-driven marketing decisions with relevant metrics and visual patterns.

### Data Analysis
This are the interesting codes i wrote in other to derive insights and make decisions
```sql
-- Including new column called average_volume
Alter table russian_alcohol_consumption
add column average_volume FLOAT;
update russian_alcohol_consumption
set average_volume= avg(wine+beer+vodka+champagne+brandy);

Alter table russian_alcohol_consumption
add column total_volume FLOAT;
update russian_alcohol_consumption
set total_volume=(wine+beer+vodka+champagne+brandy);

select *
from russian_alcohol_consumption;

-- Basic exploration --
-- Number of null values

select
	sum(case when wine is null then 1 else 0 end) as wine_nulls,
	sum(case when beer is null then 1 else 0 end) as beer_nulls,
	sum(case when vodka is null then 1 else 0 end) as vodka_nulls,
	sum(case when champagne is null then 1 else 0 end) as champagne_nulls,
	sum(case when brandy is null then 1 else 0 end) as  brandy_nulls
from russian_alcohol_consumption;

-- number of regions & years --
select count(distinct region) total_regions, count(distinct production_year) survey_years
from russian_alcohol_consumption;

-- Trend & Descriptive analysis

-- Top 10 Total drink consumption 
select region, round(sum(wine+beer+vodka+champagne+brandy),2) total_consumption
from russian_alcohol_consumption
group by region
order by 2 desc
limit 10;

-- top 10 regional average drink consumption --
select region, round(sum(total_volume)/5,2) average_consumption
from russian_alcohol_consumption
group by region
order by 2 desc	
limit 10;

-- Average drink consumption per region
select region,
	round(avg (wine),2) wine_avg,
	round(avg (beer), 2) beer_avg,
	round(avg (vodka),2) vodka_avg,
	round(avg (champagne),2) champagne_avg,
	round(avg (brandy),2) brandy_avg
from russian_alcohol_consumption
group by region
order by 1 desc;

--  Annual alcohol consumption trend --
select production_year, round(sum(total_volume),2) as annual_total_consumption
from russian_alcohol_consumption
group by production_year
order by 2 desc;

-- Annual average alcohol consumption trend -- 
select production_year, round(sum(total_volume)/5,2) as annual_average_consumption
from russian_alcohol_consumption
group by production_year
order by 2 desc;

-- alcohol profile percentage comparison
select region,
round(avg(wine)/avg(wine+beer+vodka+champagne+brandy)*100,2) as wine_pct,
round(avg(beer)/avg(wine+beer+vodka+champagne+brandy)*100,2) as beer_pct,
round(avg(vodka)/avg(wine+beer+vodka+champagne+brandy)*100,2) as vodka_pct,
round(avg(champagne)/avg(wine+beer+vodka+champagne+brandy)*100,2) as champagne_pct,
round(avg(brandy)/avg(wine+beer+vodka+champagne+brandy)*100,2) as brandy_pct
from russian_alcohol_consumption
group by region
order by 2 desc
limit 10;

-- alcohol annual growth trend --
select production_year,
	round(avg (wine),2) as wine_avg,
	round(avg (beer), 2) as beer_avg,
	round(avg (vodka),2) as vodka_avg,
	round(avg (champagne),2) as champagne_avg,
	round(avg (brandy),2) as brandy_avg
from russian_alcohol_consumption
group by production_year
order by 3 desc;

-- Annual consumption breakdown --
select production_year,
       round(sum(wine), 2) AS wine_total,
       round(sum(beer), 2) AS beer_total,
       round(sum(vodka), 2) AS vodka_total,
       round(sum(champagne), 2) AS champagne_total,
       round(sum(brandy), 2) AS brandy_total,
       round(sum(wine) + sum(beer) + sum(vodka) + sum(champagne) + sum(brandy), 2) AS total_consumption
from russian_alcohol_consumption
group by production_year
order by total_consumption desc;


-- average alcohol consumption in saint petersburg--
select 
	round(avg (wine),2) as wine_avg,
	round(avg (beer), 2) as beer_avg,
	round(avg (vodka),2) as vodka_avg,
	round(avg (champagne),2) as champagne_avg,
	round(avg (brandy),2) as brandy_avg
from russian_alcohol_consumption
where region = 'saint petersburg';

-- regions with highest alcohol consumption over time--
select 
region, round(avg(wine),2) as average_wine
from russian_alcohol_consumption
group by region
order by average_wine desc
limit 10;


-- wine annual trend for saint petersburg--
select production_year, wine
from russian_alcohol_consumption
where region='saint petersburg'
order by 2 desc;

-- regions with wine consumption similar to saint petersburg
select round(avg(wine),2) as avg_wine
from russian_alcohol_consumption
where region = 'saint petersburg';

select region, round(avg(wine),2) as avg_wine
from russian_alcohol_consumption
group by region
having avg_wine between 6.50 and 7.49 
and region != 'saint petersburg'
order by 2 desc
limit 10;


-- Top 10 regional total consumers --
select region, round(sum(wine),2) wine_total
from russian_alcohol_consumption
group by region
order by 2 desc
limit 10;

-- Top 10 regional consumers on average --
select region, round(avg(wine),2) wine_avg
from russian_alcohol_consumption
group by region
order by 2 desc
limit 10;

-- annual wine consumption trend --

select production_year, round(avg(wine),2) as annual_wine_avg
from russian_alcohol_consumption
group by production_year
order by 2 desc;

-- top 10 wine consumption percentage --
select region,
round(round(sum(wine),2)/round(sum(total_volume),2)*100,2) as wine_pct
from russian_alcohol_consumption
group by region
order by 2 desc;

-- top 10 regional wine consumption percentage similar to saint petersburg --
select region,
round(round(sum(wine),2)/round(sum(total_volume),2)*100,2) as wine_pct
from russian_alcohol_consumption
where region = 'saint petersburg';

select region,
round(round(sum(wine),2)/round(sum(total_volume),2)*100,2) as wine_pct
from russian_alcohol_consumption
group by region
having wine_pct between 6.00 and 6.99 and region != 'saint petersburg'
order by 2 desc;

-- top 10 average regional wine consumption
select region,
round(round(avg(wine),2)/round(avg(total_volume),2)*100,2) as wine_pct_avg
from russian_alcohol_consumption
group by region
order by 2 desc;

-- top 10 wine consumptiom region in current year
select region, round(avg(wine),2) as avg_wine_cnsmptn
from russian_alcohol_consumption
where production_year=2016
group by region
order by 2 desc
limit 10;

-- top 10 wine consumption region in previous year
select region, avg(wine) as avg_wine_cnsmptn
from russian_alcohol_consumption
where production_year=2015
group by region
order by 2 desc
limit 10;

-- top 10 wine consumption region in last 5 years
select region, round(avg(wine),2) as avg_wine_cnsmptn
from russian_alcohol_consumption
where production_year between 2011 and 2016
group by region
order by 2 desc
limit 10;
```

### Results 
-  Republic of Ingushetia: Despite having relatively low overall alcohol consumption, wine stands out significantly, making up nearly 34% of total alcohol intake.
This suggests a strong cultural or personal preference for wine, making it a high-potential target for our next promotion.

- Moscow: With an average wine consumption of 7.38 liters per capita and a solid wine-to-alcohol ratio, Moscow’s urban population and higher purchasing power make it an attractive market for premium wine campaigns.

Sverdlovsk Oblast: Matching Moscow in average wine consumption, but with a higher 7.77% share of total alcohol, it shows consistent and reliable wine-drinking behavior.

Kamchatka Krai: records a remarkable 8.04% of total alcohol consumption from wine.
This indicates a niche market that values wine, possibly due to limited but intentional purchasing habits.

Ivanovo Oblast: with an average of 7.24 liters per capita and a 7.71% wine share, signaling a balanced and steady preference for wine products.

Krasnodar Krai: unsurprisingly shows a strong wine culture — with wine making up over 10% of total alcohol consumption.
This region may offer not just consumers, but also valuable local partnerships.

Yaroslavl Oblast: maintaining both high per capita wine consumption and a respectable 8.24% share, confirming it as another high-performing region.

Tver Oblast: with 7.04 liters of wine per capita and nearly 9% of alcohol consumption coming from wine.
Its numbers suggest a population with refined taste and potential for premium marketing.

Chelyabinsk Oblast: with wine consumption at 6.94 liters and a 7.23% alcohol share, reflecting moderate yet consistent buying behavior.

Zabaykalsky Krai: Averaging 6.81 liters of wine and a 7.53% share, it presents another valuable opportunity to expand our promotional efforts.

### Recommendations
Based on the analysis, it is recommended that the wine promotion campaign be expanded to regions that demonstrate either a high volume of wine consumption or a strong preference for wine relative to other alcoholic beverages.

Top-performing regions such as the Republic of Ingushetia, Moscow, Sverdlovsk Oblast, and Krasnodar Krai exhibit consumption patterns similar to Saint Petersburg, our initial success benchmark. By targeting these areas, the marketing team can maximize the likelihood of campaign success, ensure better allocation of promotional resources, and tap into existing consumer preferences for wine.

### Limitations
- Cultural and Religious Differences:
Some regions, like the Republic of Ingushetia, may have high wine-to-alcohol ratios simply because other alcohol types are culturally or religiously restricted, not necessarily due to a strong wine preference.

- Data Aggregation and Reporting Gaps:
The dataset provides average consumption figures but may not reflect seasonal variations, age demographics, or income levels that influence purchasing decisions.

- Availability and Distribution Logistics:
Remote regions like Kamchatka Krai or Zabaykalsky Krai might have infrastructure challenges that affect supply chain, storage, and timely delivery of promotional stock.

- Economic Disparities:
High consumption doesn’t always translate to high purchasing power. A region might consume more affordable wine rather than premium brands — affecting the type of products that will succeed.

- Local Competition and Regulations:
Some regions may already have strong local wine brands or restrictions on alcohol advertising and promotions, limiting the campaign’s effectiveness.

- Urban vs Rural Divide:
Aggregated regional data may mask differences between urban centers (where promotions might do well) and rural areas (where interest may be lower).
