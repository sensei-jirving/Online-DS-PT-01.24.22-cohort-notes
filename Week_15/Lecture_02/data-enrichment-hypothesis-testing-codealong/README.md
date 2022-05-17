>- Repo Cloned from: https://github.com/coding-dojo-data-science/data-enrichment-hypothesis-testing-codealong
___
# Mock Belt Exam Revisited - For Class

- 05/05/05

## Original Instructions

Data Enrichment Mock Exam

API results:

https://drive.google.com/file/d/10iWPhZtId0R9RCiVculSozCwldG-V3eH/view?usp=sharing

1. Read in the json file
2. Separate the records into 4 tables each a pandas dataframe
3. Transform
    In this case remove dollar signs from funded amount in the financials records and convert to numeric datatype
4. Create a database with SQLAlchemy and add the tables to the datbase

___
5. **Perform a hypothesis test to determine if there is a signficant difference between the funded amount when it is all males and when there is at least one female in the group.**

### Follow-Up Hypothesis to Test (if there's time)

- If there is time, perform an additional hypothesis test to determine if there is a significant difference in the funded amount for different sectors.


# ETL of JSON File


```python
import json
import pandas as pd
import numpy as np
import seaborn as sns
from scipy import stats


import pymysql
pymysql.install_as_MySQLdb()

from sqlalchemy import create_engine
from sqlalchemy_utils import create_database, database_exists
```

## Extract


```python
## Loading json file
with open('Mock_Crowdsourcing_API_Results.json') as f:
    results = json.load(f)
results.keys()
```


```python
## explore each key 
type(results['meta'])
```


```python
## display meta
results['meta']
```


```python
## display data
type(results['data'])
```


```python
## preview the dictionary
# results['data']
```


```python
## preview just the keys
results['data'].keys()
```


```python
## what does the crowd key look like?
# results['data']['crowd']
```


```python
## checking single entry of crowd
results['data']['crowd'][0]
```


```python
## making crowd a dataframe
crowd = pd.DataFrame(results['data']['crowd'])
crowd
```


```python
## making demographics a dataframe
demo = pd.DataFrame(results['data']['demographics'])
demo
```


```python
## making financials a dataframe
financials = pd.DataFrame(results['data']['financials'])
financials
```


```python
## making use a dataframe
use = pd.DataFrame(results['data']['use'])
use
```

## Transform


```python
## fixing funded amount column
financials['funded_amount'] = financials['funded_amount'].str.replace('$','')
financials['funded_amount'] = pd.to_numeric(financials['funded_amount'])
financials
```

## Load


```python
## loading mysql credentials
with open('/Users/codingdojo/.secret/mysql.json') as f:
    login = json.load(f)
login.keys()
```


```python
## creating connection to database with sqlalchemy
connection_str  = f"mysql+pymysql://{login['user']}:{login['password']}@localhost/mock-belt-exam"
engine = create_engine(connection_str)
```


```python
## Check if database exists, if not, create it
if database_exists(connection_str) == False: 
    create_database(connection_str)
else: 
    print('The database already exists.')
```


```python
## saving dataframes to database
financials.to_sql('financials', engine, index=False, if_exists = 'replace')
use.to_sql('use', engine, index=False, if_exists = 'replace')
demo.to_sql('demographics', engine, index=False, if_exists = 'replace')
crowd.to_sql('crowd',engine, index=False, if_exists = 'replace')
```


```python
## checking if tables created
q= '''SHOW TABLES;'''
pd.read_sql(q,engine)
```

# Hypothesis Testing

> Follow the [Guide: Choosing the Right Hypothesis Test from the LP.](https://login.codingdojo.com/m/376/12533/88117)

### 1. State the Hypothesis & Null Hypothesis 

- $H_0$ (Null Hypothesis):
- $H_A$ (Alternative Hypothesis): 
