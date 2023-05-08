# MyNetflixData

## Case
Netflix provides users with the ability to download their watch history from the inception of their account. I downloaded my watch history in order to analyse it.

## Steps take to process this data
1.Exported data from my Netflix account

2.Created a new schema on MySQL Workbench

3.Created a new table within the new schema

4.Imported data from Netflix into the table

5.Added additional data columns to expand the dataset. 

## Findings
Based on my history I have predominately watched more shows than movies on Netflix. 

My vieweship has decreased over the years with the highest points of viewrship being during the Covid 19 pandemic. 

Maybe I should cancel my subscription.

## Process

The first step was to import the data into MySQL. I created a new Schema called 'MyNetflixData' and within that schema, I created a new table called 'watchhistory' to store my data.

```sql
CREATE SCHEMA MyNetflixData;
```

```sql
CREATE TABLE watch_history (
  title VARCHAR(255) NOT NULL,
  date DATE NOT NULL
);
```

### Output:

![Schema and Table](Dragster.jpg)

However, the data provided by Netflix is quite limited, as they only give you the title of the program and the date it was watched. I needed each movie to have a unique reference, so I created a new column called 'ID' using ALTER TABLE and added a unique id that will also function as a primary key using the  the AUTO-INCREMENT function:

```sql
ALTER TABLE watch_history
ADD COLUMN id INT NOT NULL auto_increment PRIMARY KEY;
```

To expand the dataset, I added a new column called 'category.' I set the parameters for this column to classify titles containing 'series,' 'chapter,' 'season,' 'part,' or 'episode' as TV shows, and everything else as a movie. To create a new column and populate it, I used the ALTER TABLE function:

```sql
ALTER TABLE watch_history
ADD COLUMN category VARCHAR(20);
```

```sql
UPDATE watch_history
SET category =
CASE
WHEN title LIKE'%Series%'
or title LIKE'%chapter%'
or title LIKE'%season%'
or title LIKE'%part%'
or title LIKE'%episode%'
THEN 'TV Show'
ELSE 'Movie'
END;
```



I also added a new column for runtime, using an average runtime of 101 minutes for movies and 42 minutes for TV shows, as I didn't have an exact runtime for each program. I used the following code to create and populate the column:

```sql
ALTER TABLE watch_history
ADD COLUMN runtime INT;
```

```sql
UPDATE watch_history
SET runtime =
CASE
WHEN category = 'Tv Show' THEN 42
WHEN category = 'Movie' THEN 101
ELSE NULL
END;
```

After preparing the data, I conducted some analysis. To see which category I watched the most, I used the following code:

```sql
SELECT category, COUNT(*) as Count
FROM watch_history
GROUP BY category
ORDER BY Count DESC;
```

This showed that I watched more TV shows than movies on Netflix. I also wanted to see if my viewing trend had decreased over time. To do this, I used the following code:

```sql
SELECT
YEAR(date) AS year,
SUM(CASE WHEN category = 'Movie' THEN 101 ELSE 42 END) AS watch_time_minutes
FROM watch_history
GROUP BY YEAR(date)
ORDER BY year;
```

The outcome showed a declining viewing trend since the high viewing figures of 2020 and 2021. Perhaps it's time to cancel my subscription.

