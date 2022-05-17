>- Repo Cloned from: https://github.com/coding-dojo-data-science/data-enrichment-wk14-mapping-yelp-results
___
# Mapping Yelp Search Results - Parts 1 & 2
- 04/26/22 & 04/28/22
___

# Mapping Yelp Search Results - Parts 1
- 04/26/22


## Obective

- For this CodeAlong, we will be working with the Yelp API. 
- You will use the the Yelp API to search your home town for a cuisine type of your choice.
- Next class, we will then use Plotly Express to create a map with the Mapbox API to visualize the results.
    
    

## Tools You Will Use
- Part 1:
    - Yelp API:
        - Getting Started: 
            - https://www.yelp.com/developers/documentation/v3/get_started

    - `YelpAPI` python package
        -  "YelpAPI": https://github.com/gfairchild/yelpapi
- Part 2:

    - Plotly Express: https://plotly.com/python/getting-started/
        - With Mapbox API: https://www.mapbox.com/
        - `px.scatter_mapbox` [Documentation](https://plotly.com/python/scattermapbox/): 




### Applying Code From
- Efficient API Calls Lesson Link: https://login.codingdojo.com/m/376/12529/88078


```python
# Standard Imports
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Additional Imports
import os, json, math, time
from yelpapi import YelpAPI
from tqdm.notebook import tqdm_notebook
```

## 1. Registering for Required APIs


- Yelp: https://www.yelp.com/developers/documentation/v3/get_started


> Check the official API documentation to know what arguments we can search for: https://www.yelp.com/developers/documentation/v3/business_search

### Load Credentials and Create Yelp API Object


```python
# Load API Credentials

```


```python
# Instantiate YelpAPI Variable

```

### Define Search Terms and File Paths


```python
# set our API call parameters and filename before the first call

```


```python
## Specify fodler for saving data


# Specifying JSON_FILE filename (can include a folder)
JSON_FILE = None
```

### Check if Json File exists and Create it if it doesn't


```python
## Check if JSON_FILE exists

## If it does not exist: 
    
    ## CREATE ANY NEEDED FOLDERS
    # Get the Folder Name only

    
    ## If JSON_FILE included a folder:

        # create the folder

        
        
    ## INFORM USER AND SAVE EMPTY LIST

    
    
    ## save the first page of results

        
## If it exists, inform user

```

### Load JSON FIle and account for previous results


```python
## Load previous results and use len of results for offset

## set offset based on previous results

```

### Make the first API call to get the first page of data

- We will use this first result to check:
    - how many total results there are?
    - Where is the actual data we want to save?
    - how many results do we get at a time?



```python
# use our yelp_api variable's search_query method to perform our API call

```


```python
## How many results total?

```

- Where is the actual data we want to save?


```python

```


```python
## How many did we get the details for?
results_per_page = None
results_per_page
```

- Calculate how many pages of results needed to cover the total_results


```python
# Use math.ceil to round up for the total number of pages of results.

```


```python
for i in tqdm_notebook( range(1,n_pages+1)):
    ## The block of code we want to TRY to run
        
        
        ## Read in results in progress file and check the length

        
        ## save number of results for to use as offset
        
        
        
        ## use n_results as the OFFSET 
        

        ## append new results and save to file
        

            
    ## What to do if we get an error/exception.
        

```

## Open the Final JSON File with Pandas


```python
df = None
```


```python
## convert the filename to a .csv.gz
csv_file = JSON_FILE.replace('.json','.csv.gz')
csv_file
```


```python
## Save it as a compressed csv (to save space)

```

## Bonus: compare filesize with os module's `os.path.getsize`


```python
size_json = os.path.getsize(JSON_FILE)
size_csv_gz = os.path.getsize(JSON_FILE.replace('.json','.csv.gz'))

print(f'JSON FILE: {size_json:,} Bytes')
print(f'CSV.GZ FILE: {size_csv_gz:,} Bytes')

print(f'the csv.gz is {size_json/size_csv_gz} times smaller!')
```

## Next Class: Processing the Results and Mapping 

___

# Mapping Yelp Search Results - Part 2

- 04/28/22


## Obective

- For this CodeAlong, we will be working with the Yelp API results from last class. 
- You will load in the .csv.gz of your yelp results and prepare the data for visualization.
- You will use Plotly Express to create an interactive map with all of the results.

## Tools You Will Use
- Part 1:
    - Yelp API:
        - Getting Started: 
            - https://www.yelp.com/developers/documentation/v3/get_started

    - `YelpAPI` python package
        -  "YelpAPI": https://github.com/gfairchild/yelpapi
- Part 2:

    - Plotly Express: https://plotly.com/python/getting-started/
        - With Mapbox API: https://www.mapbox.com/
        - `px.scatter_mapbox` [Documentation](https://plotly.com/python/scattermapbox/): 




### Applying Code From
- [Advanced Transformations with Pandas - Part 1](https://login.codingdojo.com/m/376/12529/88086)
- [Advanced Transformations with Pandas - Part 2](https://login.codingdojo.com/m/376/12529/88088)

### Goal

- We want to create a map with every restaurant plotted as a scatter plot with detailed information that appears when we hover over a business
- We will use plotly express's `px.scatter_mapbox` function to accomplish this.
    - https://plotly.com/python/scattermapbox/
    
    - We will need a Mapbox API token for some of the options:
        - https://studio.mapbox.com/
    

# Loading Data from Part 1


```python
## Plotly is not included in your dojo-env
!pip install plotly
```


```python
# Standard Imports
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

import json

## importing plotly 
import plotly.express as px
```


```python
## Load in csv.gz

```

## Required Preprocessing 

- 1. We need to get the latitude and longitude for each business as separate columns.
- We also want to be able to show the restaurants:
    - name,
    - price range
    - address
    - and if they do delivery or takeout.

### Separating Latitude and Longitude


```python
## use .apply pd.Series to convert a dict to columns
df['coordinates'].apply(pd.Series)
```

- Why didn't that work???


```python
## slice out a single test coordinate
test_coord = None
test_coord
```

- Its not a dictionary anymore!!! WTF??
    - CSV files cant store iterables (lists, dictionaries) so they get converted to strings.

### Fixing the String-Dictionaries

- The json module has another version of load and dump called `json.loads` and `json.dumps`
    - These are designed to process STRINGS instead of files. 
    
- If we use `json.loads` we can convert our string dictionary into an actual dictionary. 


```python
## Use json.loads on the test coordinate

```

- JSON requires double quotes!


```python
## replace single ' with " 

```


```python
## Use json.loads on the test coordinate, again

```

### Now, how can we apply this same process to the entire column??


```python
## replace ' with " (entire column)

## apply json.loads

```


```python
## slice out a single test coordinate

```

### Using Apply with pd.Series to convert a dictionary column into multiple columns


```python
## use .apply pd.Series to convert a dict to columns
df['coordinates'].apply(pd.Series)
```


```python
## Concatenate the 2 new columns and drop the original.

```

## Creating a Simple Map

### Register for MapBox API

Mapbox API: https://www.mapbox.com/


```python
## Load in mapbox api credentials from .secret

```

- Use the plotly express `set_maptbox_acccess_token` function


```python
## set mapbox token

```


```python
## use scatter_mapbox for M.V.P map

```

### Adding Hover Data

- We want to show the restaurants:
    - name
    - price range
    - address
    - and if they do delivery or takeout.
    
    
- We can use the `hover_name` and `hover_data` arguments for `px.scatter_mapbox` to add this info!


```python

```


```python
## add hover_name (name) and hover_data for price,rating,location

```

### Fixing the Location Column


```python
## slice out a test address

```

> Also a string-dictionary...


```python
## replace ' with "
df['location'] = df['location'].str.replace("'", '"')
df
```


```python
## apply json.loads

```

> Ruh roh....

- Hmm, let's slice out a test_address again and let's write a function to accomplish this instead.
    - We can use try and except in our function to get around the errors.

### Fixing Addresses - with a custom function



```python
## slice out test address 
test_addr = df.loc[0, 'location']
test_addr
```


```python
## write a function to just run json.loads on the address

```


```python
## test applying our function

```

- It worked! Now let's save this as a new column (display_location),
and then let's investigate the businesses that had an "ERROR".


```python
### save a new display_location column using our function

```


```python
## filter for businesses with display_location == "ERROR"

```


```python
## slice out a new test address and inspect
test_addr = df.loc[437, 'location']
test_addr
```

> After some more investigation, we would find a few issues with these "ERROR" rows.
1. They contained None.
2. They contained an apostrophe in the name.
3. ...?

### Possible Fixes (if we care to/have the time)


- Use Regular Expressions to find an fix the display addresses with "'" in them
- Use string split to split on the word display address.
    - Then use string methods to clean up

### Moving Forward without those rows (for now)


```python
## remove any rows where display_location == 'ERROR'

```

- We want the "display_address" key from the "display_location" dictionaries.
- We could use a .apply and a lamda to slice out the desired key.


```python
## use apply and lambda to slice correct key

```

- Almost done! We want to convert display_address to a string instead a list of strings.
- We can use the string method .join to do so!


```python
## slice out a test_address

```


```python
## test using .join with a "\n"

```


```python
## apply the join to every row with a lambda

```

### Final Map


```python
## make ourn final map and save as varaible

```

#### HTML Uses `<br>` instead of `\n`


```python
## remake the final address column with <br> instead 

## plot the final map
```


```python
## use fig.write_html to save map

```




