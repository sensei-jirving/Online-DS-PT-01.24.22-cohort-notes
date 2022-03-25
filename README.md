# Online-DS-PT-01-24-22-cohort-notes

<img src="./images/Data Science Thumbnail.png">

## Important Links
- [Lecture Recordings Playlist](https://youtube.com/playlist?list=PLmeeqPbYmMC0XlmuN4agv0zvuAXP8HZS_)
- [Stack 1 Schedule](https://bit.ly/32k7fwU)
- [Stack 2 Schedule](https://docs.google.com/spreadsheets/d/1_HT2W_o4VvwFQ1kJBaPSivKGh9feFFdNA9wSvOolg1c/edit?usp=sharing)
- [Stack 3 Schedule](https://docs.google.com/spreadsheets/d/17knl47MBv-ETynjkLv5vVcGHDuEzVy1gyYyztBjpWkM/edit?usp=sharing)

## Tips and Tricks

### Pandas `pd.read_excel` error.

- If you get an error message that mentions openpyxl when you attempt to to run `pd.read_excel`:
	- Add this line of code at the very beginning of your notebook (before import pandas) and restart your kernel. 
	- Menu Bar > `Runtime` > `Restart Runtime`
```python
!pip install openpyxl==3.0.0
```
