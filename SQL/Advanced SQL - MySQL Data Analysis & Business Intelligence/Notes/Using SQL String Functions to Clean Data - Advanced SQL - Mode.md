> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [mode.com](https://mode.com/sql-tutorial/sql-string-functions-for-cleaning/)

> This lesson of the SQL tutorial for data analysis covers SQL string functions to clean data strings a...... 

## [[SQL with Mosh#2 字符串函数 - String Functions]]

**Starting here?** This lesson is part of a full-length tutorial in using SQL for Data Analysis. [Check out the beginning](https://mode.com/sql-tutorial/introduction-to-sql).

**In this lesson we'll cover:**

*   [Cleaning strings](https://mode.com/sql-tutorial/sql-string-functions-for-cleaning/#cleaning-strings)
*   [LEFT, RIGHT, and LENGTH](https://mode.com/sql-tutorial/sql-string-functions-for-cleaning/#left-right-and-length)
*   [TRIM](https://mode.com/sql-tutorial/sql-string-functions-for-cleaning/#trim)
*   [POSITION and STRPOS](https://mode.com/sql-tutorial/sql-string-functions-for-cleaning/#position-and-strpos)
*   [SUBSTR](https://mode.com/sql-tutorial/sql-string-functions-for-cleaning/#substr)
*   [CONCAT](https://mode.com/sql-tutorial/sql-string-functions-for-cleaning/#concat)
*   [Changing case with UPPER and LOWER](https://mode.com/sql-tutorial/sql-string-functions-for-cleaning/#changing-case-with-upper-and-lower)
*   [Turning strings into dates](https://mode.com/sql-tutorial/sql-string-functions-for-cleaning/#turning-strings-into-dates)
*   [Turning dates into more useful dates](https://mode.com/sql-tutorial/sql-string-functions-for-cleaning/#turning-dates-into-more-useful-dates)
*   [COALESCE](https://mode.com/sql-tutorial/sql-string-functions-for-cleaning/#coalesce)

This lesson features data on San Francisco Crime Incidents for the 3-month period beginning November 1, 2013 and ending January 31, 2014. It was collected from the [SF Data website](https://data.sfgov.org/Public-Safety/Police-Department-Incident-Reports-Historical-2003/tmnf-yvry/data) on February 16, 2014. There is one row for each incident reported. Some field definitions: `location` is the GPS location of the incident, listed in [decimal degrees](http://en.wikipedia.org/wiki/Decimal_degrees), latitude first, longitude second. The two coordinates are also broken out into the `lat` and `lon` fields, respectively.

Start by taking a look:

```
SELECT *
  FROM tutorial.sf_crime_incidents_2014_01
```

Cleaning strings
-----------------------------------------------------------------------------------------------------

Most of the functions presented in this lesson are specific to certain data types. However, using a particular function will, in many cases, change the data to the appropriate type. `LEFT`, `RIGHT`, and `TRIM` are all used to select only certain elements of strings, but using them to select elements of a number or date will treat them as strings for the purpose of the function.

### LEFT, RIGHT, and LENGTH

Let's start with `LEFT`. You can use `LEFT` to pull a certain number of characters from the left side of a string and present them as a separate string. The syntax is `LEFT(string, number of characters)`.

As a practical example, we can see that the `date` field in this dataset begins with a 10-digit date, and include the timestamp to the right of it. The following query pulls out only the ogimage: "/images/og-images/sql-facebook.png" date:

```
SELECT incidnt_num,
       date,
       LEFT(date, 10) AS cleaned_date
  FROM tutorial.sf_crime_incidents_2014_01
```

`RIGHT` does the same thing, but from the right side:

```
SELECT incidnt_num,
       date,
       LEFT(date, 10) AS cleaned_date,
       RIGHT(date, 17) AS cleaned_time
  FROM tutorial.sf_crime_incidents_2014_01
```

`RIGHT` works well in this case because we know that the number of characters will be consistent across the entire `date` field. If it wasn't consistent, it's still possible to pull a string from the right side in a way that makes sense. The `LENGTH` function returns the length of a string. So `LENGTH(date)` will always return `28` in this dataset. Since we know that the first 10 characters will be the date, and they will be followed by a space (total 11 characters), we could represent the `RIGHT` function like this:

```
SELECT incidnt_num,
       date,
       LEFT(date, 10) AS cleaned_date,
       RIGHT(date, LENGTH(date) - 11) AS cleaned_time
  FROM tutorial.sf_crime_incidents_2014_01
```

When using functions within other functions, it's important to remember that the innermost functions will be evaluated first, followed by the functions that encapsulate them.

### TRIM

**The `TRIM` function is used to remove characters from the beginning and/or end of a string.** Here's an example:

```
SELECT location,
       TRIM(both '()' FROM location)
  FROM tutorial.sf_crime_incidents_2014_01
```

The `TRIM` function takes 3 arguments. 
>First, you have to specify whether you want to remove characters from the beginning ('leading'), the end ('trailing'), or both ('both', as used above). Next you must specify all characters to be trimmed. Any characters included in the single quotes will be removed from both beginning, end, or both sides of the string. Finally, you must specify the text you want to trim using `FROM`.

### POSITION and STRPOS

`POSITION` allows you to specify a substring, then returns a numerical value equal to the character number (counting from left) where that substring **first appears** in the target string. For example, the following query will return the position of the character 'A' (case-sensitive) where it first appears in the `descript` field:

```
SELECT incidnt_num,
       descript,
       POSITION('A' IN descript) AS a_position
  FROM tutorial.sf_crime_incidents_2014_01
```

You can also use the `STRPOS` function to achieve the same results—just replace `IN` with a comma and switch the order of the string and substring:

```
SELECT incidnt_num,
       descript,
       STRPOS(descript, 'A') AS a_position
  FROM tutorial.sf_crime_incidents_2014_01
```

**Importantly, both the `POSITION` and `STRPOS` functions are case-sensitive. If you want to look for a character regardless of its case, you can make your entire string a single by using the `UPPER` or `LOWER` functions described below.**


## Turning strings into dates #dates 


### Dates are some of the most commonly screwed-up formats in SQL. This can be the result of a few things:

*   The data was manipulated in Excel at some point, and the dates were changed to MM/DD/YYYY format or another format that is not compliant with SQL's strict standards.
*   The data was manually entered by someone who use whatever formatting convention he/she was most familiar with.
*   The date uses text (Jan, Feb, etc.) intsead of numbers to record months.

In order to take advantage of all of the great date functionality (`INTERVAL`, as well as some others you will learn in the next section), you need to have your date field formatted appropriately. This often involves some text manipulation, followed by a `CAST`. Let's revisit the answer to one of the practice problems above:

🔆
```SQL
SELECT incidnt_num,
       date,
       (SUBSTR(date, 7, 4) || '-' || LEFT(date, 2) ||
        '-' || SUBSTR(date, 4, 2))::date AS cleaned_date
  FROM tutorial.sf_crime_incidents_2014_01
```

This example is a little different from the answer above in that we've wrapped the entire set of concatenated substrings in parentheses and cast the result in the `date` format. We could also cast it as `timestamp`, which includes additional precision (hours, minutes, seconds). In this case, we're not pulling the hours out of the original field, so we'll just stick to `date`.

Practice Problem
----------------

Write a query that creates an accurate timestamp using the `date` and `time` columns in `tutorial.sf_crime_incidents_2014_01`. Include a field that is exactly 1 week later as well.

[Try it out](https://app.mode.com/editor/reports/new) [See the answer](https://app.mode.com/tutorial/reports/4c908f47868a/queries/ac2923250b9c)

## Turning dates into more useful dates


You've learned how to construct a date field, but what if you want to deconstruct one? You can use `EXTRACT` to pull the pieces apart one-by-one:

```sql
SELECT cleaned_date,
       EXTRACT('year'   FROM cleaned_date) AS year,
       EXTRACT('month'  FROM cleaned_date) AS month,
       EXTRACT('day'    FROM cleaned_date) AS day,
       EXTRACT('hour'   FROM cleaned_date) AS hour,
       EXTRACT('minute' FROM cleaned_date) AS minute,
       EXTRACT('second' FROM cleaned_date) AS second,
       EXTRACT('decade' FROM cleaned_date) AS decade,
       EXTRACT('dow'    FROM cleaned_date) AS day_of_week
  FROM tutorial.sf_crime_incidents_cleandate
```

**You can also round dates to the nearest unit of measurement.** This is particularly useful if you don't care about an individual date, but do care about the week (or month, or quarter) that it occurred in. The `DATE_TRUNC` function rounds a date to whatever precision you specify. The value displayed is the first value in that period. So when you `DATE_TRUNC` by year, any value in that year will be listed as January 1st of that year:

```sql
SELECT cleaned_date,
       DATE_TRUNC('year'   , cleaned_date) AS year,
       DATE_TRUNC('month'  , cleaned_date) AS month,
       DATE_TRUNC('week'   , cleaned_date) AS week,
       DATE_TRUNC('day'    , cleaned_date) AS day,
       DATE_TRUNC('hour'   , cleaned_date) AS hour,
       DATE_TRUNC('minute' , cleaned_date) AS minute,
       DATE_TRUNC('second' , cleaned_date) AS second,
       DATE_TRUNC('decade' , cleaned_date) AS decade
  FROM tutorial.sf_crime_incidents_cleandate
```

Practice Problem
----------------

Write a query that counts the number of incidents reported by week. Cast the week as a date to get rid of the hours/minutes/seconds.

[Try it out](https://app.mode.com/editor/reports/new) [See the answer](https://app.mode.com/tutorial/reports/0315a8aa1e4c/queries/d629812ef9d1)

What if you want to include today's date or time? You can instruct your query to pull the local date and time at the time the query is run using any number of functions. Interestingly, you can run them without a `FROM` clause:

```
SELECT CURRENT_DATE AS date,
       CURRENT_TIME AS time,
       CURRENT_TIMESTAMP AS timestamp,
       LOCALTIME AS localtime,
       LOCALTIMESTAMP AS localtimestamp,
       NOW() AS now
```

As you can see, the different options vary in precision. You might notice that these times probably aren't actually your local time. Mode's database is set to [Coordinated Universal Time](http://en.wikipedia.org/wiki/Coordinated_Universal_Time) (UTC), which is basically the same as GMT. If you run a current time function against a connected database, you might get a result in a different time zone.

You can make a time appear in a different time zone using `AT TIME ZONE`:

```
SELECT CURRENT_TIME AS time,
       CURRENT_TIME AT TIME ZONE 'PST' AS time_pst
```

For a complete list of timezones, [look here](http://www.postgresql.org/docs/7.2/static/timezones.html). This functionality is pretty complex because timestamps can be stored with or without timezone metadata. For a better understanding of the exact syntax, we recommend checking out the [Postgres documentation](http://www.postgresql.org/docs/9.2/static/functions-datetime.html#FUNCTIONS-DATETIME-ZONECONVERT).

Practice Problem
----------------

Write a query that shows exactly how long ago each indicent was reported. Assume that the dataset is in Pacific Standard Time (UTC - 8).

[Try it out](https://app.mode.com/editor/reports/new) [See the answer](https://app.mode.com/tutorial/reports/ebc77b3a1dd7/queries/cea0f7dd2a09)

[](https://mode.com/sql-tutorial/sql-string-functions-for-cleaning/#coalesce)COALESCE
-------------------------------------------------------------------------------------

Occasionally, you will end up with a dataset that has some nulls that you'd prefer to contain actual values. This happens frequently in numerical data (displaying nulls as 0 is often preferable), and when performing outer joins that result in some unmatched rows. In cases like this, you can use `COALESCE` to replace the null values:

```
SELECT incidnt_num,
       descript,
       COALESCE(descript, 'No Description')
  FROM tutorial.sf_crime_incidents_cleandate
 ORDER BY descript DESC
```