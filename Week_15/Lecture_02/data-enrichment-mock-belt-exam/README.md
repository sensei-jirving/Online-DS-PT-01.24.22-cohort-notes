>- Repo Cloned from: https://github.com/coding-dojo-data-science/data-enrichment-mock-belt-exam
___
# Mock Belt Exam - Data Enrichment


- Example Solution File
- 05/03/22
- James Irving

## Instructions

Data Enrichment Mock Exam

API results:

https://drive.google.com/file/d/10iWPhZtId0R9RCiVculSozCwldG-V3eH/view?usp=sharing

1. Read in the json file
2. Separate the records into 4 tables each a pandas dataframe
3. Transform
    In this case remove dollar signs from funded amount in the financials records and convert to numeric datatype
4. Create a database with SQLAlchemy and add the tables to the datbase
5. Perform a hypothesis test to determine if there is a signficant difference between the funded amount when it is all males and when there is at least one female in the group.

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




    dict_keys(['meta', 'data'])




```python
## explore each key 
type(results['meta'])
```




    str




```python
## display meta
results['meta']
```




    'Practice Lesson: Mock API Call'




```python
## display data
type(results['data'])
```




    dict




```python
## preview the dictionary
# results['data']
```


```python
## preview just the keys
results['data'].keys()
```




    dict_keys(['crowd', 'demographics', 'financials', 'use'])




```python
## what does the crowd key look like?
# results['data']['crowd']
```


```python
## checking single entry of crowd
results['data']['crowd'][0]
```




    {'id': 658776,
     'posted_time': '2014-01-17 21:21:10+00:00',
     'funded_time': '2014-02-05 17:57:55+00:00',
     'lender_count': 33}




```python
## making crowd a dataframe
crowd = pd.DataFrame(results['data']['crowd'])
crowd
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>posted_time</th>
      <th>funded_time</th>
      <th>lender_count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>658776</td>
      <td>2014-01-17 21:21:10+00:00</td>
      <td>2014-02-05 17:57:55+00:00</td>
      <td>33</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1314847</td>
      <td>2017-06-07 02:02:41+00:00</td>
      <td>2017-06-21 17:10:38+00:00</td>
      <td>9</td>
    </tr>
    <tr>
      <th>2</th>
      <td>863063</td>
      <td>2015-03-27 20:08:04+00:00</td>
      <td>2015-04-04 15:01:22+00:00</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1184347</td>
      <td>2016-11-14 07:32:12+00:00</td>
      <td>2016-11-25 03:07:13+00:00</td>
      <td>47</td>
    </tr>
    <tr>
      <th>4</th>
      <td>729745</td>
      <td>2014-06-24 07:35:46+00:00</td>
      <td>2014-07-10 16:12:43+00:00</td>
      <td>12</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>9995</th>
      <td>679499</td>
      <td>2014-03-05 07:05:38+00:00</td>
      <td>2014-03-13 01:01:41+00:00</td>
      <td>11</td>
    </tr>
    <tr>
      <th>9996</th>
      <td>873525</td>
      <td>2015-04-22 06:32:13+00:00</td>
      <td>None</td>
      <td>6</td>
    </tr>
    <tr>
      <th>9997</th>
      <td>917686</td>
      <td>2015-07-15 11:53:33+00:00</td>
      <td>2015-08-14 11:45:40+00:00</td>
      <td>44</td>
    </tr>
    <tr>
      <th>9998</th>
      <td>905789</td>
      <td>2015-06-22 07:44:18+00:00</td>
      <td>2015-07-14 00:20:45+00:00</td>
      <td>11</td>
    </tr>
    <tr>
      <th>9999</th>
      <td>1216411</td>
      <td>2017-01-06 06:54:07+00:00</td>
      <td>2017-01-08 01:17:28+00:00</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
<p>10000 rows × 4 columns</p>
</div>




```python
## making demographics a dataframe
demo = pd.DataFrame(results['data']['demographics'])
demo
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>country</th>
      <th>region</th>
      <th>borrower_genders</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>658776</td>
      <td>El Salvador</td>
      <td>Ciudad El Triunfo</td>
      <td>male</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1314847</td>
      <td>Philippines</td>
      <td>Bais, Negros Oriental</td>
      <td>female</td>
    </tr>
    <tr>
      <th>2</th>
      <td>863063</td>
      <td>Peru</td>
      <td>Huarochiri</td>
      <td>female, female, female, female, female, female...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1184347</td>
      <td>Armenia</td>
      <td>Vanadzor town</td>
      <td>female</td>
    </tr>
    <tr>
      <th>4</th>
      <td>729745</td>
      <td>Uganda</td>
      <td>Masindi</td>
      <td>female</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>9995</th>
      <td>679499</td>
      <td>Pakistan</td>
      <td>Lahore</td>
      <td>female</td>
    </tr>
    <tr>
      <th>9996</th>
      <td>873525</td>
      <td>Kenya</td>
      <td>Machakos</td>
      <td>male, male, female, female, male</td>
    </tr>
    <tr>
      <th>9997</th>
      <td>917686</td>
      <td>Senegal</td>
      <td>None</td>
      <td>female, female</td>
    </tr>
    <tr>
      <th>9998</th>
      <td>905789</td>
      <td>Philippines</td>
      <td>Binalbagan, Negros Occidental</td>
      <td>female</td>
    </tr>
    <tr>
      <th>9999</th>
      <td>1216411</td>
      <td>Philippines</td>
      <td>Carmen, Bohol</td>
      <td>female</td>
    </tr>
  </tbody>
</table>
<p>10000 rows × 4 columns</p>
</div>




```python
## making financials a dataframe
financials = pd.DataFrame(results['data']['financials'])
financials
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>funded_amount</th>
      <th>currency</th>
      <th>term_in_months</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>658776</td>
      <td>$1000.0</td>
      <td>USD</td>
      <td>20.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1314847</td>
      <td>$225.0</td>
      <td>PHP</td>
      <td>13.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>863063</td>
      <td>$1150.0</td>
      <td>PEN</td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1184347</td>
      <td>$1700.0</td>
      <td>AMD</td>
      <td>26.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>729745</td>
      <td>$400.0</td>
      <td>UGX</td>
      <td>8.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>9995</th>
      <td>679499</td>
      <td>400.0</td>
      <td>PKR</td>
      <td>12.0</td>
    </tr>
    <tr>
      <th>9996</th>
      <td>873525</td>
      <td>375.0</td>
      <td>KES</td>
      <td>14.0</td>
    </tr>
    <tr>
      <th>9997</th>
      <td>917686</td>
      <td>1375.0</td>
      <td>XOF</td>
      <td>8.0</td>
    </tr>
    <tr>
      <th>9998</th>
      <td>905789</td>
      <td>450.0</td>
      <td>PHP</td>
      <td>13.0</td>
    </tr>
    <tr>
      <th>9999</th>
      <td>1216411</td>
      <td>125.0</td>
      <td>PHP</td>
      <td>16.0</td>
    </tr>
  </tbody>
</table>
<p>10000 rows × 4 columns</p>
</div>




```python
## making use a dataframe
use = pd.DataFrame(results['data']['use'])
use
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>activity</th>
      <th>sector</th>
      <th>use</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>658776</td>
      <td>Vehicle</td>
      <td>Personal Use</td>
      <td>to purchase a motorcycle in order to travel fr...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1314847</td>
      <td>Pigs</td>
      <td>Agriculture</td>
      <td>to buy feed and other supplies like vitamins t...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>863063</td>
      <td>Bookstore</td>
      <td>Retail</td>
      <td>to buy notebooks, pencils, and pens.</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1184347</td>
      <td>Photography</td>
      <td>Services</td>
      <td>to pay for a new lens for providing photograph...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>729745</td>
      <td>Fuel/Firewood</td>
      <td>Retail</td>
      <td>to buy  firewood to sell.</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>9995</th>
      <td>679499</td>
      <td>Fruits &amp; Vegetables</td>
      <td>Food</td>
      <td>to help her husband buy onions for resale.</td>
    </tr>
    <tr>
      <th>9996</th>
      <td>873525</td>
      <td>Farming</td>
      <td>Agriculture</td>
      <td>to buy fertilizer and pesticides to boost his ...</td>
    </tr>
    <tr>
      <th>9997</th>
      <td>917686</td>
      <td>Fish Selling</td>
      <td>Food</td>
      <td>buy fish</td>
    </tr>
    <tr>
      <th>9998</th>
      <td>905789</td>
      <td>General Store</td>
      <td>Retail</td>
      <td>to buy more groceries to sell.</td>
    </tr>
    <tr>
      <th>9999</th>
      <td>1216411</td>
      <td>Personal Housing Expenses</td>
      <td>Housing</td>
      <td>to buy cement, hollow blocks, GI sheets, sand,...</td>
    </tr>
  </tbody>
</table>
<p>10000 rows × 4 columns</p>
</div>



## Transform


```python
## fixing funded amount column
financials['funded_amount'] = financials['funded_amount'].str.replace('$','')
financials['funded_amount'] = pd.to_numeric(financials['funded_amount'])
financials
```

    /var/folders/rf/vw4r41jd7vd95x1w0dth7v9h0000gp/T/ipykernel_61654/2638807975.py:2: FutureWarning: The default value of regex will change from True to False in a future version. In addition, single character regular expressions will *not* be treated as literal strings when regex=True.
      financials['funded_amount'] = financials['funded_amount'].str.replace('$','')





<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>funded_amount</th>
      <th>currency</th>
      <th>term_in_months</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>658776</td>
      <td>1000.0</td>
      <td>USD</td>
      <td>20.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1314847</td>
      <td>225.0</td>
      <td>PHP</td>
      <td>13.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>863063</td>
      <td>1150.0</td>
      <td>PEN</td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1184347</td>
      <td>1700.0</td>
      <td>AMD</td>
      <td>26.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>729745</td>
      <td>400.0</td>
      <td>UGX</td>
      <td>8.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>9995</th>
      <td>679499</td>
      <td>400.0</td>
      <td>PKR</td>
      <td>12.0</td>
    </tr>
    <tr>
      <th>9996</th>
      <td>873525</td>
      <td>375.0</td>
      <td>KES</td>
      <td>14.0</td>
    </tr>
    <tr>
      <th>9997</th>
      <td>917686</td>
      <td>1375.0</td>
      <td>XOF</td>
      <td>8.0</td>
    </tr>
    <tr>
      <th>9998</th>
      <td>905789</td>
      <td>450.0</td>
      <td>PHP</td>
      <td>13.0</td>
    </tr>
    <tr>
      <th>9999</th>
      <td>1216411</td>
      <td>125.0</td>
      <td>PHP</td>
      <td>16.0</td>
    </tr>
  </tbody>
</table>
<p>10000 rows × 4 columns</p>
</div>



## Load


```python
## loading mysql credentials
with open('/Users/codingdojo/.secret/mysql.json') as f:
    login = json.load(f)
login.keys()
```




    dict_keys(['user', 'password'])




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

    The database already exists.



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




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Tables_in_mock-belt-exam</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>crowd</td>
    </tr>
    <tr>
      <th>1</th>
      <td>demographics</td>
    </tr>
    <tr>
      <th>2</th>
      <td>financials</td>
    </tr>
    <tr>
      <th>3</th>
      <td>use</td>
    </tr>
  </tbody>
</table>
</div>



# Hypothesis Testing

### State the Hypothesis & Null Hypothesis 

- $H_0$ (Null Hypothesis): Funded amount is the same for teams that contain at least 1 female and teams that are all male.
- $H_A$ (Alternative Hypothesis): There is a significant difference between the funded amount for teams that contain at least 1 female and teams that are all male.

- Based upon the [Choosing the Right Hypothesis Test workflow from  the LP]( https://login.codingdojo.com/m/376/12533/88117):
    - **The appropriate test to perform would be:**
        - Since we are measuring a numeric quantity (funded amount)
        - and we are comparing 2 groups/samples. 
        - We therefore want to perform a 2-sample t-test, A.K.A. an independent t-test.
    
- **According the the work flow, the 2-sample T-Test has the following assumptions:** 
    - No significant outliers
    - Normality
    - Equal Variance

## Getting the Group Data 

- The next step is to get the data for each group in separate variables. All of the approaches below will lead to the same result: a `male_df` and `female_df` variable.

### Approach 1: Using the MySQL Database to Get DF to Filter


```python
q = """SELECT 
    f.id, f.funded_amount, d.borrower_genders
FROM
    financials AS f
        JOIN
    demographics AS d ON f.id = d.id;"""
df = pd.read_sql(q,engine)
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>funded_amount</th>
      <th>borrower_genders</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>658776</td>
      <td>1000.0</td>
      <td>male</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1314847</td>
      <td>225.0</td>
      <td>female</td>
    </tr>
    <tr>
      <th>2</th>
      <td>863063</td>
      <td>1150.0</td>
      <td>female, female, female, female, female, female...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1184347</td>
      <td>1700.0</td>
      <td>female</td>
    </tr>
    <tr>
      <th>4</th>
      <td>729745</td>
      <td>400.0</td>
      <td>female</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>9995</th>
      <td>1033255</td>
      <td>1000.0</td>
      <td>male</td>
    </tr>
    <tr>
      <th>9996</th>
      <td>998024</td>
      <td>150.0</td>
      <td>female</td>
    </tr>
    <tr>
      <th>9997</th>
      <td>771844</td>
      <td>225.0</td>
      <td>female</td>
    </tr>
    <tr>
      <th>9998</th>
      <td>679499</td>
      <td>400.0</td>
      <td>female</td>
    </tr>
    <tr>
      <th>9999</th>
      <td>917686</td>
      <td>1375.0</td>
      <td>female, female</td>
    </tr>
  </tbody>
</table>
<p>10000 rows × 3 columns</p>
</div>




```python
## Create a column that defines the 2 groups, has female or not
df['has_female'] = df['borrower_genders'].str.contains('female', case=False)
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>funded_amount</th>
      <th>borrower_genders</th>
      <th>has_female</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>658776</td>
      <td>1000.0</td>
      <td>male</td>
      <td>False</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1314847</td>
      <td>225.0</td>
      <td>female</td>
      <td>True</td>
    </tr>
    <tr>
      <th>2</th>
      <td>863063</td>
      <td>1150.0</td>
      <td>female, female, female, female, female, female...</td>
      <td>True</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1184347</td>
      <td>1700.0</td>
      <td>female</td>
      <td>True</td>
    </tr>
    <tr>
      <th>4</th>
      <td>729745</td>
      <td>400.0</td>
      <td>female</td>
      <td>True</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>9995</th>
      <td>1033255</td>
      <td>1000.0</td>
      <td>male</td>
      <td>False</td>
    </tr>
    <tr>
      <th>9996</th>
      <td>998024</td>
      <td>150.0</td>
      <td>female</td>
      <td>True</td>
    </tr>
    <tr>
      <th>9997</th>
      <td>771844</td>
      <td>225.0</td>
      <td>female</td>
      <td>True</td>
    </tr>
    <tr>
      <th>9998</th>
      <td>679499</td>
      <td>400.0</td>
      <td>female</td>
      <td>True</td>
    </tr>
    <tr>
      <th>9999</th>
      <td>917686</td>
      <td>1375.0</td>
      <td>female, female</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
<p>10000 rows × 4 columns</p>
</div>




```python
## Separate the column of interest based on the groups
male_df = df.loc[ df['has_female']==False, ['funded_amount','has_female']]
female_df = df.loc[ df['has_female']==True, ['funded_amount','has_female']]
print(f"There are {len(female_df)} campaigns that had females on the team." )
print(f"There are {len(male_df)} campaigns that only had males on the team." )
```

    There are 7820 campaigns that had females on the team.
    There are 2119 campaigns that only had males on the team.


### Approach 2: Using the MySQL database to make the male_df and female_df

>- Due to a quirk with using "%" with sqlalchemy queries, in order to use a LIKE command with "%" for "%female":
    1. Add quotation marks around the "%" expression. 
    ```python
    q = '''SELECT 
    f.funded_amount, d.borrower_genders
FROM
    financials AS f
        JOIN
    demographics AS d ON f.id = d.id
WHERE
    d.borrower_genders LIKE "%female%";'''
    ```
    2. Use the sqlalchemy text function when running your query. 
    ```python
    from sqlalchemy import text
    female_df = pd.read_sql(text(q),engine)
    ```


```python
## importing text function to use on query with a "%" in it
from sqlalchemy import text
```


```python
## query to get campaigns that included female borrowers
q = '''SELECT 
    f.funded_amount,  
    d.borrower_genders LIKE "%female%" as "has_female"
FROM
    financials AS f
        JOIN
    demographics AS d ON f.id = d.id
WHERE
    d.borrower_genders LIKE "%female%";'''
female_df = pd.read_sql(text(q),engine)
female_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>funded_amount</th>
      <th>has_female</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>225.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1150.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1700.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>400.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>350.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>7815</th>
      <td>400.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>7816</th>
      <td>375.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>7817</th>
      <td>1375.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>7818</th>
      <td>450.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>7819</th>
      <td>125.0</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
<p>7820 rows × 2 columns</p>
</div>




```python
## query to get campaigns that were only male borrowers
q = """SELECT 
    f.funded_amount, 
    d.borrower_genders LIKE "%female%" as "has_female"

FROM
    financials AS f
        JOIN
    demographics AS d ON f.id = d.id
WHERE
    d.borrower_genders NOT LIKE '%female%';"""
male_df = pd.read_sql(text(q),engine)
male_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>funded_amount</th>
      <th>has_female</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1000.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>925.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>875.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>600.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>375.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2114</th>
      <td>1000.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2115</th>
      <td>800.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2116</th>
      <td>125.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2117</th>
      <td>100.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2118</th>
      <td>3000.0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>2119 rows × 2 columns</p>
</div>




```python
print(f"There are {len(female_df)} campaigns that had females on the team." )
print(f"There are {len(male_df)} campaigns that only had males on the team." )
```

    There are 7820 campaigns that had females on the team.
    There are 2119 campaigns that only had males on the team.


### Approach 3: Use pd.merge to join the DataFrames


```python
df = pd.merge(financials, demo, on='id')
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>funded_amount</th>
      <th>currency</th>
      <th>term_in_months</th>
      <th>country</th>
      <th>region</th>
      <th>borrower_genders</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>658776</td>
      <td>1000.0</td>
      <td>USD</td>
      <td>20.0</td>
      <td>El Salvador</td>
      <td>Ciudad El Triunfo</td>
      <td>male</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1314847</td>
      <td>225.0</td>
      <td>PHP</td>
      <td>13.0</td>
      <td>Philippines</td>
      <td>Bais, Negros Oriental</td>
      <td>female</td>
    </tr>
    <tr>
      <th>2</th>
      <td>863063</td>
      <td>1150.0</td>
      <td>PEN</td>
      <td>6.0</td>
      <td>Peru</td>
      <td>Huarochiri</td>
      <td>female, female, female, female, female, female...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1184347</td>
      <td>1700.0</td>
      <td>AMD</td>
      <td>26.0</td>
      <td>Armenia</td>
      <td>Vanadzor town</td>
      <td>female</td>
    </tr>
    <tr>
      <th>4</th>
      <td>729745</td>
      <td>400.0</td>
      <td>UGX</td>
      <td>8.0</td>
      <td>Uganda</td>
      <td>Masindi</td>
      <td>female</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>9995</th>
      <td>679499</td>
      <td>400.0</td>
      <td>PKR</td>
      <td>12.0</td>
      <td>Pakistan</td>
      <td>Lahore</td>
      <td>female</td>
    </tr>
    <tr>
      <th>9996</th>
      <td>873525</td>
      <td>375.0</td>
      <td>KES</td>
      <td>14.0</td>
      <td>Kenya</td>
      <td>Machakos</td>
      <td>male, male, female, female, male</td>
    </tr>
    <tr>
      <th>9997</th>
      <td>917686</td>
      <td>1375.0</td>
      <td>XOF</td>
      <td>8.0</td>
      <td>Senegal</td>
      <td>None</td>
      <td>female, female</td>
    </tr>
    <tr>
      <th>9998</th>
      <td>905789</td>
      <td>450.0</td>
      <td>PHP</td>
      <td>13.0</td>
      <td>Philippines</td>
      <td>Binalbagan, Negros Occidental</td>
      <td>female</td>
    </tr>
    <tr>
      <th>9999</th>
      <td>1216411</td>
      <td>125.0</td>
      <td>PHP</td>
      <td>16.0</td>
      <td>Philippines</td>
      <td>Carmen, Bohol</td>
      <td>female</td>
    </tr>
  </tbody>
</table>
<p>10000 rows × 7 columns</p>
</div>




```python
df['has_female'] = df['borrower_genders'].str.contains('female', case=False)
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>funded_amount</th>
      <th>currency</th>
      <th>term_in_months</th>
      <th>country</th>
      <th>region</th>
      <th>borrower_genders</th>
      <th>has_female</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>658776</td>
      <td>1000.0</td>
      <td>USD</td>
      <td>20.0</td>
      <td>El Salvador</td>
      <td>Ciudad El Triunfo</td>
      <td>male</td>
      <td>False</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1314847</td>
      <td>225.0</td>
      <td>PHP</td>
      <td>13.0</td>
      <td>Philippines</td>
      <td>Bais, Negros Oriental</td>
      <td>female</td>
      <td>True</td>
    </tr>
    <tr>
      <th>2</th>
      <td>863063</td>
      <td>1150.0</td>
      <td>PEN</td>
      <td>6.0</td>
      <td>Peru</td>
      <td>Huarochiri</td>
      <td>female, female, female, female, female, female...</td>
      <td>True</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1184347</td>
      <td>1700.0</td>
      <td>AMD</td>
      <td>26.0</td>
      <td>Armenia</td>
      <td>Vanadzor town</td>
      <td>female</td>
      <td>True</td>
    </tr>
    <tr>
      <th>4</th>
      <td>729745</td>
      <td>400.0</td>
      <td>UGX</td>
      <td>8.0</td>
      <td>Uganda</td>
      <td>Masindi</td>
      <td>female</td>
      <td>True</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>9995</th>
      <td>679499</td>
      <td>400.0</td>
      <td>PKR</td>
      <td>12.0</td>
      <td>Pakistan</td>
      <td>Lahore</td>
      <td>female</td>
      <td>True</td>
    </tr>
    <tr>
      <th>9996</th>
      <td>873525</td>
      <td>375.0</td>
      <td>KES</td>
      <td>14.0</td>
      <td>Kenya</td>
      <td>Machakos</td>
      <td>male, male, female, female, male</td>
      <td>True</td>
    </tr>
    <tr>
      <th>9997</th>
      <td>917686</td>
      <td>1375.0</td>
      <td>XOF</td>
      <td>8.0</td>
      <td>Senegal</td>
      <td>None</td>
      <td>female, female</td>
      <td>True</td>
    </tr>
    <tr>
      <th>9998</th>
      <td>905789</td>
      <td>450.0</td>
      <td>PHP</td>
      <td>13.0</td>
      <td>Philippines</td>
      <td>Binalbagan, Negros Occidental</td>
      <td>female</td>
      <td>True</td>
    </tr>
    <tr>
      <th>9999</th>
      <td>1216411</td>
      <td>125.0</td>
      <td>PHP</td>
      <td>16.0</td>
      <td>Philippines</td>
      <td>Carmen, Bohol</td>
      <td>female</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
<p>10000 rows × 8 columns</p>
</div>




```python
## Separate the column of interest based on the groups
male_df = df.loc[ df['has_female']==False, ['funded_amount','has_female']]
female_df = df.loc[ df['has_female']==True,['funded_amount','has_female']]

print(f"There are {len(female_df)} campaigns that had females on the team." )
print(f"There are {len(male_df)} campaigns that only had males on the team." )
```

    There are 7820 campaigns that had females on the team.
    There are 2119 campaigns that only had males on the team.


## Visualize Group Means


```python
## concatenate the two dataframes for visualziation.
plot_df = pd.concat([male_df, female_df], axis=0)
plot_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>funded_amount</th>
      <th>has_female</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1000.0</td>
      <td>False</td>
    </tr>
    <tr>
      <th>8</th>
      <td>925.0</td>
      <td>False</td>
    </tr>
    <tr>
      <th>18</th>
      <td>875.0</td>
      <td>False</td>
    </tr>
    <tr>
      <th>22</th>
      <td>600.0</td>
      <td>False</td>
    </tr>
    <tr>
      <th>32</th>
      <td>375.0</td>
      <td>False</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>9995</th>
      <td>400.0</td>
      <td>True</td>
    </tr>
    <tr>
      <th>9996</th>
      <td>375.0</td>
      <td>True</td>
    </tr>
    <tr>
      <th>9997</th>
      <td>1375.0</td>
      <td>True</td>
    </tr>
    <tr>
      <th>9998</th>
      <td>450.0</td>
      <td>True</td>
    </tr>
    <tr>
      <th>9999</th>
      <td>125.0</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
<p>9939 rows × 2 columns</p>
</div>




```python
## visualizing means. ci=68 makes easier to compare error bars (will discuss in class)
ax = sns.barplot(data=plot_df, x='has_female', y='funded_amount', ci=68)
```


    
![png](Images/output_49_0.png)
    



```python
## now that we have visualized the groups, we can save a final male_group and female_group
# that are a pandas Series. This will make the rest of our workflow simpler than if 
# we still had a dataframe

female_group = female_df['funded_amount']
male_group = male_df['funded_amount']
display(female_group.head(), male_group.head())
```


    1     225.0
    2    1150.0
    3    1700.0
    4     400.0
    5     350.0
    Name: funded_amount, dtype: float64



    0     1000.0
    8      925.0
    18     875.0
    22     600.0
    32     375.0
    Name: funded_amount, dtype: float64


## Checking Assumptions of 2-Sample T-test

- **According the the work flow, the 2-sample T-Test has the following assumptions:** 
    - No significant outliers
    - Normality
    - Equal Variance

### Checking for Outliers

- Check each group SEPARATELY!


```python
## Checking for abs vlaue of z-scores that are > 3
is_outlier_females = np.abs(stats.zscore(female_group)) > 3
print(f"There are {is_outlier_females.sum()} outliers in the female group out of {len(female_group)})")
```

    There are 202 outliers in the female group out of 7820)



```python
female_df.loc[~is_outlier_females]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>funded_amount</th>
      <th>has_female</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>225.0</td>
      <td>True</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1150.0</td>
      <td>True</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1700.0</td>
      <td>True</td>
    </tr>
    <tr>
      <th>4</th>
      <td>400.0</td>
      <td>True</td>
    </tr>
    <tr>
      <th>5</th>
      <td>350.0</td>
      <td>True</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>9995</th>
      <td>400.0</td>
      <td>True</td>
    </tr>
    <tr>
      <th>9996</th>
      <td>375.0</td>
      <td>True</td>
    </tr>
    <tr>
      <th>9997</th>
      <td>1375.0</td>
      <td>True</td>
    </tr>
    <tr>
      <th>9998</th>
      <td>450.0</td>
      <td>True</td>
    </tr>
    <tr>
      <th>9999</th>
      <td>125.0</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
<p>7618 rows × 2 columns</p>
</div>




```python
## removing outliers from female_group
female_group = female_group.loc[~is_outlier_females]
female_group
```




    1        225.0
    2       1150.0
    3       1700.0
    4        400.0
    5        350.0
             ...  
    9995     400.0
    9996     375.0
    9997    1375.0
    9998     450.0
    9999     125.0
    Name: funded_amount, Length: 7618, dtype: float64




```python
## Checking for abs vlaue of z-scores that are > 3
is_outlier_males = np.abs(stats.zscore(male_group)) > 3
print(f"There are {is_outlier_males.sum()} outliers in the male group of out of {len(male_group)}.")
```

    There are 26 outliers in the male group of out of 2119.



```python
## removing outliers from male_group
male_group = male_group.loc[~is_outlier_males]
male_group
```




    0       1000.0
    8        925.0
    18       875.0
    22       600.0
    32       375.0
             ...  
    9984    1000.0
    9985     800.0
    9991     125.0
    9992     100.0
    9993    3000.0
    Name: funded_amount, Length: 2093, dtype: float64



### Checking for Normality

- According to the workflow on the LP, since both groups have n > 15, we can safely ignore the assumption of normality.

### Checking for Equal Variance


```python
result = stats.levene(male_group, female_group)
print(result)
print(result.pvalue<.05)
```

    LeveneResult(statistic=5.919603200045773, pvalue=0.014991261165002913)
    True


- According to the documentation for stats.levene, the null hypothesis for the test is that both groups have equal variance. Since our p-value is less than .05 we reject that null hypothesis and conclude that our groups do NOT have equal variance. 

- Since we did NOT meet the assumption of equal variance, we will run our stats.ttest_ind using equal_var=False. This will run a Welch's T-Test, which is designed to account for unequal variance. 

## Statistical Test 


```python
result = stats.ttest_ind(male_group, female_group)
print(result)
result.pvalue < .05
```

    Ttest_indResult(statistic=4.48964981000983, pvalue=7.216307212906817e-06)





    True



## Final Conclusion

- Our Welch's T-Test return a p-value < .05 (it was actually p <.0001!) we reject the null hypothesis and support the alternative hypothesis that there is a significant difference in funded amounts for teams that included at least 1 female. 

- In order to know if they are funded significantly MORE or LESS, we look at the actual means of our final groups.


```python
print(f"The average funded_amount for male groups was {male_group.mean():.2f}")
print(f"The average funded_amount for female groups was {female_group.mean():.2f}")
```

    The average funded_amount for male groups was 712.06
    The average funded_amount for female groups was 640.80


- Male groups are funded at significantly higher amounts than female groups. 

## OPTIONAL - VIEWING THE BARPLOT WITHOUT OUTLIERS


```python
## concatenate the two dataframes for visualziation.
plot_df = pd.concat([male_df.loc[~is_outlier_males], 
                     female_df.loc[~is_outlier_females]], axis=0)
plot_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>funded_amount</th>
      <th>has_female</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1000.0</td>
      <td>False</td>
    </tr>
    <tr>
      <th>8</th>
      <td>925.0</td>
      <td>False</td>
    </tr>
    <tr>
      <th>18</th>
      <td>875.0</td>
      <td>False</td>
    </tr>
    <tr>
      <th>22</th>
      <td>600.0</td>
      <td>False</td>
    </tr>
    <tr>
      <th>32</th>
      <td>375.0</td>
      <td>False</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>9995</th>
      <td>400.0</td>
      <td>True</td>
    </tr>
    <tr>
      <th>9996</th>
      <td>375.0</td>
      <td>True</td>
    </tr>
    <tr>
      <th>9997</th>
      <td>1375.0</td>
      <td>True</td>
    </tr>
    <tr>
      <th>9998</th>
      <td>450.0</td>
      <td>True</td>
    </tr>
    <tr>
      <th>9999</th>
      <td>125.0</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
<p>9711 rows × 2 columns</p>
</div>




```python
sns.barplot(data=plot_df, x='has_female',y='funded_amount')
```




    <AxesSubplot:xlabel='has_female', ylabel='funded_amount'>




    
![png](Images/output_73_1.png)
    

