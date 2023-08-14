# DATA ANALYSIS on Random Data
## Excel Part
  __First I made 3 worksheets under 'random data' into 3 distinct workbook.
  In the zip_data workbook I found that Hammonton city had duplicate zip value(8037) error but having diff population 
  so I continued with the first entered data and deleted it in MS Excel!
  I changed the workbook file format to csv from xlsx.__
  >View the datasets given here: [Data_Analysis_Dataset](https://github.com/Abirgit44/Data_Analysis_Assignment_random_data/tree/master/Data_Given_for_Assignment)


## SQL Part
__**𝑺𝑸𝑳 𝒒𝒖𝒆𝒓𝒚 𝒇𝒐𝒓 𝒅𝒂𝒕𝒂 𝒂𝒏𝒂𝒍𝒚𝒔𝒊𝒔:**__

### Basic Queries
```SQL
create table zip_data(city varchar(20),	state_name varchar(20),	zip_code integer UNIQUE,	zip_code_population integer)
CREATE TABLE house_data(id VARCHAR(30) PRIMARY KEY,home_type VARCHAR(30),bed integer,bath integer,play_ground varchar(10),swimming_tank varchar(10),acre_lot float4,city varchar(20),state_name varchar(20),zip_code integer,house_size integer, foreign key(zip_code) REFERENCES zip_data(zip_code))
CREATE TABLE date_data(id VARCHAR(30),	listing_date date,	listing_price integer,	sold_date date,	actual_sold_price integer,	status varchar(15),foreign key(id) REFERENCES house_data(id))



select*from zip_data;
select*from house_data;
select*from date_data;



--searching for duplicate/unforced error values:

select id,city, state_name from house_data where zip_code=8037;
---updating the hudson county zip_code
UPDATE house_data SET zip_code=(select distinct(zip_code) from house_data where city='Hudson County' and zip_code<>8037) where city='Hudson County' and zip_code=8037;
---select zip_code from house_data where city= 'Hudson County';
---so pincode got updated

select*from house_data;
```
__**At first I checked the Entity Relationship Diagram of the given dataset; here is a pic of that:**__
![image](https://github.com/Abirgit44/Data_Analysis_Assignment_random_data/assets/111905512/9dcfa2d1-acd0-46ec-ae80-e9f8f04d8425)


**Now we will check details about certain type of homes**
__Also will be creating subqueries to make more classifications__

>FIRST Query answers the issue of finding total number of Single Family houses in each city in New Jersey having atleast 1 bed and bath:
  ```sql
  SELECT COUNT(id),city,bed FROM house_data 
  WHERE home_type='Single Family' and bath>0 and bed>0
  GROUP BY city,bed
  ORDER BY bed desc;
  ```
ANSWER: [Query Output](https://github.com/Abirgit44/Data_Analysis_Assignment_random_data/blob/master/All_SQL_Findings_except_Joining/sing_fam_total_in_each_city_with_bednbath.csv)

>SECOND Query answers the issue of finding total number of Land houses in each city in New Jersey having atleast 1 bed and bath:
```sql
  SELECT COUNT(id),city,bed FROM house_data 
  WHERE home_type='Land' and bath>0 and bed>0
  GROUP BY city,bed
  ORDER BY bed desc;
```
ANSWER: [Query Output](https://github.com/Abirgit44/Data_Analysis_Assignment_random_data/blob/master/All_SQL_Findings_except_Joining/land_total_in_each_city_with_bednbath.csv)

>THIRD Query answers the issue of finding total number of MULTI Family houses in each city in New Jersey having atleast 1 bed and bath:
```sql
SELECT COUNT(id),city,bed FROM house_data 
WHERE home_type='Multi Family' and bath>0 and bed>0
GROUP BY city,bed
ORDER BY bed desc;
```
ANSWER: [Query Output](https://github.com/Abirgit44/Data_Analysis_Assignment_random_data/blob/master/All_SQL_Findings_except_Joining/mul_fam_total_in_each_city_with_bednbath.csv)

> The following queries answer the number of total houses with 3 different kind of homes:
  ```sql
  select COUNT(*) from house_data WHERE home_type='Single Family';
  ---output: 169
  select COUNT(*) from house_data WHERE home_type='Land';
  ---output: 162
  select COUNT(*) from house_data WHERE home_type='Multi Family';
  ---output: 173
  ```
>**one thing can be important i.e. for people thinking about opening business or creating office, they might choose the houses with no bed, atleast 1 bath and more than avg area:**
  ```sql
  select COUNT(id),home_type,city from house_data where bath>1 and bed=0 and house_size > (SELECT avg(house_size) 
  from house_data)
  GROUP BY city,home_type;
  ```
ANSWER: 
[Query Output](https://github.com/Abirgit44/Data_Analysis_Assignment_random_data/blob/master/All_SQL_Findings_except_Joining/houses_preferable_for_business.csv)

### Amenities check
```sql
---With everything:
select COUNT(id),home_type,city from house_data where bath>0 and bed>0 and play_ground='Y' and swimming_tank='Y'
GROUP BY city,home_type;
--total:92

---With everything except bath:
select COUNT(id),home_type,city from house_data where bath=0 and bed>0 and play_ground='Y' and swimming_tank='Y'
GROUP BY city,home_type;
--total:20

---With everything except bed:
select COUNT(id),home_type,city from house_data where bath>0 and bed=0 and play_ground='Y' and swimming_tank='Y'
GROUP BY city,home_type;
--total:13

---With everything except play ground:
select COUNT(id),home_type,city from house_data where bath>0 and bed>0 and play_ground='N' and swimming_tank='Y'
GROUP BY city,home_type;
--total:77

---With everything except swimming tank:
select COUNT(id),home_type,city from house_data where bath>0 and bed>0 and play_ground='Y' and swimming_tank='N'
GROUP BY city,home_type;
--total:93
```

>__**Zipdata analysis**__
  ```sql
  select*from zip_data 
  order by zip_code_population desc;
  ```
ANSWER: [Query Output](https://github.com/Abirgit44/Data_Analysis_Assignment_random_data/blob/master/All_SQL_Findings_except_Joining/zip_data_descpopulation.csv)

>__**Some analysis on date data**__
  ```sql
    ---highest actual sold value first
    select*from date_data where status='Sold' ORDER BY actual_sold_price desc;
  ```
ANSWER: [Query Output](https://github.com/Abirgit44/Data_Analysis_Assignment_random_data/blob/master/All_SQL_Findings_except_Joining/date_data_highest_actl_sold_val_first_and_sld.csv)
  ```sql
    ---newest actual sold value first
    select*from date_data where status='Sold' ORDER BY sold_date desc;
  ```
ANSWER: [Query Output](https://github.com/Abirgit44/Data_Analysis_Assignment_random_data/blob/master/All_SQL_Findings_except_Joining/date_data_newest_first_and_sld.csv)
  
 ```sql
    --latest sale date and highest actual sold amount:
    select sold_date from date_data where status='Sold' and sold_date =(SELECT sold_date from date_data ORDER BY sold_date desc LIMIT 1);
    --output:"2021-03-05"
    select actual_sold_price from date_data where status='Sold' order by actual_sold_price desc limit 1;
    --output:499481

    --total sold and pending figures
    select count(*) from date_data where status='Sold';
    --output:249
    select count(*) from date_data where status='Pending';
    --output:255
```

### JOIN:
__**𝙉𝒐𝙬 𝙬𝒆 𝒘𝙞𝒍𝙡 𝙨𝒕𝙖𝒓𝙩 𝙟𝒐𝙞𝒏𝙞𝒏𝙜:**__

>**FIRST JOIN ALL MAJOR INFORMATIONs together:**
```sql
  SELECT
      DISTINCT(h.id),
      h.home_type,
      h.bed,
      h.bath,
      h.play_ground,
      h.swimming_tank,
      h.acre_lot,
      h.house_size,
      z.city,
      z.state_name,
      z.zip_code,
      z.zip_code_population,
   d.listing_date,
      d.listing_price,
      d.sold_date,
      d.actual_sold_price,
      d.status
  FROM
      zip_data z
  JOIN
      house_data h ON h.city = z.city
  JOIN
      date_data d ON d.id=h.id;
```
Answer: [Query Output](https://github.com/Abirgit44/Data_Analysis_Assignment_random_data/blob/master/All_SQL_Findings_except_Joining/ALL_TABLE_INSIGHT_TOGETHER.csv)

__**Other findings:**__ 
>1. Average listing price by property type:
 ```sql 
  SELECT
      h.home_type,
      AVG(d.listing_price) AS avg_listing_price
  FROM
      house_data h JOIN date_data d on d.id=h.id
  GROUP BY
      h.home_type;
```
Answer:
| home_type       | avg_listing_price           |
|-----------------|-----------------------------|
| "Multi Family"  | 255885.601156069364         |
| "Land"          |         250400.759259259259 |
| "Single Family" | 254450.568047337278         |


>2. Total actual sold price by city:
  
```sql
    SELECT
        h.city,
        SUM(d.actual_sold_price) AS total_sold_price
    FROM
        date_data d
    JOIN
        house_data h ON h.id=d.id
    GROUP BY
        h.city;
  ```
Answer:
| **"city"**          | **"total_sold_price"** |
|---------------------|------------------------|
| "Atlantic City"     |     42470778           |
| "Hudson County"     |     17068342           |
| "Hammonton"         |         14056999       |
| "Basking Ridge"     |     13654176           |
| "Elizabeth"         |         42005538       |


>3. Property type distribution by state and city:
```sql
  SELECT
      z.state_name,
      z.city,
      h.home_type,
      COUNT(*) AS property_count
  FROM
      house_data h
  JOIN
      zip_data z ON h.zip_code = z.zip_code
  GROUP BY
      z.state_name,
      z.city,
      h.home_type;
```
Answer:
| **"state_name"** | **"city"**      | **"home_type"** | **"property_count"** |
|------------------|-----------------|-----------------|----------------------|
| "New Jersey"     | "Elizabeth"     | "Single Family" | 48                   |
| "New Jersey"     | "Hammonton"     | "Multi Family"  | 18                   |
| "New Jersey"     | "Basking Ridge" | "Single Family" | 14                   |
| "New Jersey"     | "Elizabeth"     | "Land"          | 62                   |
| "New Jersey"     | "Hudson County" | "Land"          | 24                   |
| "New Jersey"     | "Basking Ridge" | "Multi Family"  | 20                   |
| "New Jersey"     | "Basking Ridge" | "Land"          | 20                   |
| "New Jersey"     | "Hammonton"     | "Land"          | 16                   |
| "New Jersey"     | "Atlantic City" | "Single Family" | 57                   |
| "New Jersey"     | "Hudson County" | "Single Family" | 24                   |
| "New Jersey"     | "Hudson County" | "Multi Family"  | 19                   |
| "New Jersey"     | "Elizabeth"     | "Multi Family"  | 53                   |
| "New Jersey"     | "Atlantic City" | "Land"          | 40                   |
| "New Jersey"     | "Hammonton"     | "Single Family" | 26                   |
| "New Jersey"     | "Atlantic City" | "Multi Family"  | 63                   |


>4. Average listing price by city and state:
```sql
    SELECT
        z.state_name,
        z.city,
        AVG(d.listing_price) AS avg_listing_price
    FROM
        date_data d
    JOIN
        house_data h ON d.id = h.id
    JOIN
        zip_data z ON h.zip_code = z.zip_code
    GROUP BY
        z.state_name,
        z.city;
```
Answer:
| **"state_name"** | **"city"**      | **"avg_listing_price"** |
|------------------|-----------------|-------------------------|
| "New Jersey"     | "Elizabeth"     | 239060.533742331288     |
| "New Jersey"     | "Hudson County" | 253470.656716417910     |
| "New Jersey"     | "Hammonton"     | 250193.050000000000     |
| "New Jersey"     | "Atlantic City" | 266602.768750000000     |
| "New Jersey"     | "Basking Ridge" | 263293.537037037037     |


>5. Number of properties with a playground by city:
```sql
  SELECT
      z.state_name,
      z.city,
      SUM(CASE WHEN h.play_ground = 'Y' THEN 1 ELSE 0 END) AS playground_count
  FROM
      house_data h
  JOIN
      zip_data z ON h.zip_code = z.zip_code
  GROUP BY
      z.state_name,
      z.city;
```
Answer:
| **"state_name"** | **"city"**      | **"playground_count"** |
|------------------|-----------------|------------------------|
| "New Jersey"     | "Elizabeth"     | 89                     |
| "New Jersey"     | "Hudson County" | 30                     |
| "New Jersey"     | "Hammonton"     | 32                     |
| "New Jersey"     | "Atlantic City" | 91                     |
| "New Jersey"     | "Basking Ridge" | 28                     |

>6. Average listing price by year:
```sql
  SELECT
      EXTRACT(YEAR FROM d.listing_date) AS listing_year,
      AVG(d.listing_price) AS avg_listing_price
  FROM
      date_data d
  JOIN
      house_data h ON d.id = h.id
  GROUP BY
      EXTRACT(YEAR FROM d.listing_date)
  ORDER BY
      listing_year;
```
Answer:
| **"listing_year"** | **"avg_listing_price"** |
|--------------------|-------------------------|
| 2020               | 254416.455913978495     |
| 2021               | 244400.666666666667     |

>7. Property count and average property size by zip code:
```sql
  SELECT
      z.zip_code,
      COUNT(*) AS property_count,
      AVG(h.house_size) AS avg_property_size
  FROM
      house_data h
  JOIN
      zip_data z ON h.zip_code = z.zip_code
  GROUP BY
      z.zip_code
  ORDER BY
      z.zip_code;
```
Answer:
| **"zip_code"** | **"property_count"** | **"avg_property_size"** |
|----------------|----------------------|-------------------------|
| 7208           | 53                   | 1331.5094339622641509   |
| 7215           | 49                   | 1493.3469387755102041   |
| 7216           | 61                   | 1666.8852459016393443   |
| 7305           | 67                   | 1358.8507462686567164   |
| 7920           | 54                   | 1487.3518518518518519   |
| 8037           | 60                   | 1672.3666666666666667   |
| 8402           | 49                   | 1600.3673469387755102   |
| 8404           | 64                   | 1586.4843750000000000   |
| 8405           | 47                   | 1588.8297872340425532   |

>8. Average listing price by city and day of the week:
```sql  SELECT
      z.city,
      EXTRACT(DOW FROM d.listing_date) AS day_of_week,
      AVG(d.listing_price) AS avg_listing_price
  FROM
      date_data d
  JOIN
      house_data h ON d.id = h.id
  JOIN
      zip_data z ON h.zip_code = z.zip_code
  GROUP BY
      z.city,
      day_of_week
  ORDER BY
      z.city,
      day_of_week;
```
Answer:
| **"city"**      | **"day_of_week"** | **"avg_listing_price"** |
|-----------------|-------------------|-------------------------|
| "Atlantic City" | 0                 | 243754.300000000000     |
| "Atlantic City" | 1                 | 243143.681818181818     |
| "Atlantic City" | 2                 | 234324.928571428571     |
| "Atlantic City" | 3                 | 282554.222222222222     |
| "Atlantic City" | 4                 | 299243.461538461538     |
| "Atlantic City" | 5                 | 272983.040000000000     |
| "Atlantic City" | 6                 | 266068.346153846154     |
| "Basking Ridge" | 0                 | 274432.400000000000     |
| "Basking Ridge" | 1                 | 246508.444444444444     |
| "Basking Ridge" | 2                 | 288809.500000000000     |
| "Basking Ridge" | 3                 | 240423.500000000000     |
| "Basking Ridge" | 4                 | 316343.166666666667     |
| "Basking Ridge" | 5                 | 265277.700000000000     |
| "Basking Ridge" | 6                 | 190219.600000000000     |
| "Elizabeth"     | 0                 | 234145.481481481481     |
| "Elizabeth"     | 1                 | 155827.714285714286     |
| "Elizabeth"     | 2                 | 244187.562500000000     |
| "Elizabeth"     | 3                 | 281187.681818181818     |
| "Elizabeth"     | 4                 | 247186.931034482759     |
| "Elizabeth"     | 5                 | 259998.476190476190     |
| "Elizabeth"     | 6                 | 227700.941176470588     |
| "Hammonton"     | 0                 | 265724.083333333333     |
| "Hammonton"     | 1                 | 326743.571428571429     |
| "Hammonton"     | 2                 | 176218.437500000000     |
| "Hammonton"     | 3                 | 232536.625000000000     |
| "Hammonton"     | 4                 | 287080.777777777778     |
| "Hammonton"     | 5                 | 196660.333333333333     |
| "Hammonton"     | 6                 | 336438.600000000000     |
| "Hudson County" | 0                 | 287493.750000000000     |
| "Hudson County" | 1                 | 296963.818181818182     |
| "Hudson County" | 2                 | 307273.222222222222     |
| "Hudson County" | 3                 | 191070.615384615385     |
| "Hudson County" | 4                 | 185661.000000000000     |
| "Hudson County" | 5                 | 288059.833333333333     |
| "Hudson County" | 6                 | 196724.300000000000     |


>9. Property type distribution by month and year:
```sql  SELECT:
      EXTRACT(MONTH FROM d.listing_date) AS month,
      EXTRACT(YEAR FROM d.listing_date) AS year,
      h.home_type,
      COUNT(*) AS property_count
  FROM
      date_data d
  JOIN
      house_data h ON d.id = h.id
  GROUP BY
      EXTRACT(MONTH FROM d.listing_date),
      EXTRACT(YEAR FROM d.listing_date),
      h.home_type
  ORDER BY
      year,
      month;
```
Answer:
| **"month"** | **"year"** | **"home_type"** | **"property_count"** |
|-------------|------------|-----------------|----------------------|
| 1           | 2020       | "Land"          | 7                    |
| 1           | 2020       | "Multi Family"  | 17                   |
| 1           | 2020       | "Single Family" | 15                   |
| 2           | 2020       | "Land"          | 7                    |
| 2           | 2020       | "Multi Family"  | 9                    |
| 2           | 2020       | "Single Family" | 17                   |
| 3           | 2020       | "Land"          | 15                   |
| 3           | 2020       | "Multi Family"  | 16                   |
| 3           | 2020       | "Single Family" | 13                   |
| 4           | 2020       | "Land"          | 14                   |
| 4           | 2020       | "Multi Family"  | 9                    |
| 4           | 2020       | "Single Family" | 14                   |
| 5           | 2020       | "Land"          | 14                   |
| 5           | 2020       | "Multi Family"  | 14                   |
| 5           | 2020       | "Single Family" | 12                   |
| 6           | 2020       | "Land"          | 11                   |
| 6           | 2020       | "Multi Family"  | 12                   |
| 6           | 2020       | "Single Family" | 12                   |
| 7           | 2020       | "Land"          | 13                   |
| 7           | 2020       | "Multi Family"  | 11                   |
| 7           | 2020       | "Single Family" | 15                   |
| 8           | 2020       | "Land"          | 19                   |
| 8           | 2020       | "Multi Family"  | 14                   |
| 8           | 2020       | "Single Family" | 13                   |
| 9           | 2020       | "Land"          | 18                   |
| 9           | 2020       | "Multi Family"  | 14                   |
| 9           | 2020       | "Single Family" | 8                    |
| 10          | 2020       | "Land"          | 11                   |
| 10          | 2020       | "Multi Family"  | 15                   |
| 10          | 2020       | "Single Family" | 13                   |
| 11          | 2020       | "Land"          | 11                   |
| 11          | 2020       | "Multi Family"  | 14                   |
| 11          | 2020       | "Single Family" | 7                    |
| 12          | 2020       | "Land"          | 11                   |
| 12          | 2020       | "Multi Family"  | 16                   |
| 12          | 2020       | "Single Family" | 14                   |
| 1           | 2021       | "Land"          | 10                   |
| 1           | 2021       | "Multi Family"  | 12                   |
| 1           | 2021       | "Single Family" | 16                   |
| 3           | 2021       | "Land"          | 1                    |


>10. City with the highest average property size and its population:
```sql  SELECT
      z.city,
      z.state_name,
      AVG(h.house_size) AS avg_property_size,
      z.zip_code_population
  FROM
      house_data h
  JOIN
      zip_data z ON h.zip_code = z.zip_code
  GROUP BY
      z.city,
      z.state_name,
      z.zip_code_population
  ORDER BY
      avg_property_size DESC
  LIMIT 1;
```
Answer:
| **"city"**  | **"state_name"** | **"avg_property_size"** | **"zip_code_population"** |
|-------------|------------------|-------------------------|---------------------------|
| "Hammonton" | "New Jersey"     | 1672.3666666666666667   | 23729                     |

## TABLEAU Part
### Basic steps:
>First we will connect to the datasets present to be analyzed; then we will check the data. Ignore the null values and continue.
Extract the data before publishing the dashboard.
  >>Note: **In my case it is very important to mention that I didn't find something very out of sight to be analyzed and put into dashboard of tableau from the 'zip_data', so I didn't make separate dashboard on that.**

### House_Data Analaysis Dashboard:
![Dashboard_Image](https://github.com/Abirgit44/Data_Analysis_Assignment_random_data/blob/master/House_Data_Findings_Dashboard.png)
You will find the dashboard at:[Click_me](https://public.tableau.com/app/profile/abir.maiti/viz/House_Data_Findings_Workbook/House_Data_Findings_Dashboard)

### Date_Data Analaysis Dashboard:
![Dashboard_Image](https://github.com/Abirgit44/Data_Analysis_Assignment_random_data/blob/master/Date%20Data%20Key%20findings%20Dashboard.png)
You will find the dashboard at:[Click_me](https://public.tableau.com/app/profile/abir.maiti/viz/Date_Data_Analysis_Workbook/DateDataKeyfindingsDashboard)

### Data Analaysis Dashboard after joining House, Date and Zip data:
![Dashboard_Image](https://github.com/Abirgit44/Data_Analysis_Assignment_random_data/blob/master/Dashboard_after_joining_tableau.png)
You will find the dashboard at:[Click_me](https://public.tableau.com/app/profile/abir.maiti/viz/InsightsafterjoiningTables/Dashboard_after_joining)
