# Analytics-Engineer-Assessment-SnapCommerce

Given the following string, we're required to import the data contained into a table, and clean it

```python
data = 'Airline Code;DelayTimes;FlightCodes;To_From\nAir Canada (!);[21, 40];20015.0;WAterLoo_NEWYork\n<Air France> (12);[];;Montreal_TORONTO\n(Porter Airways. );[60, 22, 87];20035.0;CALgary_Ottawa\n12. Air France;[78, 66];;Ottawa_VANcouvER\n""".\\.Lufthansa.\\.""";[12, 33];20055.0;london_MONTreal\n'
```

---
First, we import the required libraries
**Note that the pandasql library may need to be installed using the command prompt -
* go to the command prompt
* enter 'pip install pandasql'

```python
import pandas as pd
import os
from io import StringIO
import re
import numpy as np
import pandasql as ps
```

---

Then we read the given string into a table using the 'StringIO' function from the io library, and the 'read_csv' function from the Pandas library
```python
data1 = StringIO(data)

df = pd.read_csv(data1, sep=";")

df
```

---
### Airline Code column clean-up

We define a function to clean up the Airline Code column. It removes all characters except for alphabets and spaces. It also trims the leading and trailing whitespaces
```python
def Airline_code_cleaner(x):
    return re.sub(r'[^a-zA-Z ]+', '', x).strip()

# use the map() function to apply it along the column.

df['Airline Code'] = list(map(Airline_code_cleaner, df['Airline Code']))

df
```

---

### To_From column clean-up

We split the To_From column values on the '\_' character. Then, we plug these split values into two new columns - 'To' and 'From' respectively.
```python
df['To_From'] = list(map(lambda x : x.split('_'), df['To_From']))

df[['To','From']] = pd.DataFrame(df.To_From.tolist(), index= df.index)

# Dropping the 'To_From' column, since it is now redundant.

df.drop('To_From', axis = 1, inplace = True)
```

Next, we define a function to convert the entries into 'To' and 'From' columns into upper case and we also remove any leading/trailing whitespaces present.
```python
def case_cleaner(x):
    return x.upper().strip()

df['To'] = list(map(case_cleaner, df['To']))
df['From'] = list(map(case_cleaner, df['From']))

df
```

---

### FlightCodes column clean-up

We interpolate the missing values using the 'interpolate()' function, and then re-cast the values to int type

```python
df['FlightCodes'] = df['FlightCodes'].interpolate().astype(int)

df
```

---

##SQL QUERY FOR ALL FLIGHTS LEAVING *FROM* WATERLOO

We use the pandasql library to run this query - **you maybe required to install this library from the cmd prompt if it is not present in your python installation**

This is the query in SQL - Note the usage of square brackets in the WHERE clause to escape the FROM keyword
```SQL
SELECT * FROM df WHERE [From] = 'WATERLOO'
```
running the query on python

```python
query = "SELECT * FROM df WHERE [From] = 'WATERLOO'"
ps.sqldf(query)
```

**Note that this query won't produce any outputs since we have no flights in our data departing from Waterloo**
