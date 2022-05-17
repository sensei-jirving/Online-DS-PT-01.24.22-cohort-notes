>- Repo Cloned from: https://github.com/coding-dojo-data-science/data-enrichment-wk14-l01-json-activity
___
# JSON & APIs Activity - Solution

<img src="Images/youll-be-back-plus-cast.jpg"  width=70%> 
<center><a href="http://images.genius.com/bfcf74e4a3ff5662ca4a31700590a1de.1000x330x1.jpg">Image Source</a></center>

## JSON

### Reading JSON files

Import JSON library. Also, import Pandas.


Load JSON from file ('spotify_song_analysis.json'). This file contains song data from Spotify on You'll Be Back from the musical Hamilton.


```python
import json
import pandas as pd


json_file='' # enter the correct filepath for the json file.

## Use with open and the json module to load in the data.

```

Print out data.


```python

```

Check type of data, get keys.


```python

```


```python

```

Explore 'meta' by printing it out, checking its type and keys.


```python

```


```python

```

Now use the 'track' key to inspect details about the song.
- What key is the song in?


```python

```

| ID | Key   |
|------|------|
| 0 | C |
1 |	C♯ 
2  | D 
3  | D♯
4  | E 
5  | F 
6  | F♯
7  | G
8  | G♯
9  | A
10 | A♯	
11 | B 	


```python

```

How long is the song?


```python

```

Load the 'track' data into a DataFrame.


```python

```


```python

```

### Using the 'spotify_song_analysis.json' file and what you have learned above, do the following:

#### Create a DataFrame of the different segments of the song


```python

```


```python

```

#### Find the longest segment and the loudest segment.


```python

```


```python

```


```python

```


```python

```

#### How many bars are in the song?


```python

```

#### What's the average length of a bar?


```python

```

### Save your DataFrame of Segments to a new JSON file using pandas

- Use `df.to_json()` with:
    - The name of new json file. Something like "Data/song_segments.json"
    - Add "orient='records'" to save the data as a list of dictionaries.


```python

```

#### Use the open function and json module to load in your new json file and preview the first entry.


```python

```
