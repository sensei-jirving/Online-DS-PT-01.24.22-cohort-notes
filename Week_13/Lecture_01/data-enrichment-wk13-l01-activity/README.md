>- Repo Cloned from: https://github.com/coding-dojo-data-science/data-enrichment-wk13-l01-activity
___
# data-enrichment-wk13-l01-activity
 
>For today’s activity, you are going to be walking through all of the steps required for your first Core assignment, but with a different database.

1. Fork and clone this GitHub repository using GitHub Desktop.
	- The owner of the repository is displayed as part of the repo name on GitHub.com. If it says "coding-dojo-data-science", you are looking at the original repo. Click on the "Fork" button on the top-right.
	
	<img src="Images/fork_repo_1.png" width=600px>
	
	- You will see the name of the repo will change and should now start with YOUR GitHub username. It will also indicate that it is a fork of the original repository. 
	
	<img src="Images/fork_repo_2.png" width=600px>
	
	
2. Open the repo with Jupyter Notebook and create a new Notebook using dojo-env for your analysis. [Lesson Link](https://login.codingdojo.com/m/376/12528/88059) (See the "Open the Repository with Jupyter Notbeook" section.)
	- Don't see Python (dojo-env) as an option? 
		- Make sure you ran the following line in your terminal/GitBash from [Python Installation Step 2](https://login.codingdojo.com/m/376/12807/89878) after creating your dojo-env: 
		```python -m ipykernel install --user --name dojo-env --display-name "Python (dojo-env)"```
3. Install the included database in the Data folder (Chinook_MySql.sql) into your MySQL Server. 
	- Either double-click on the .sql file in the Data folder using Windows' File Explorer or Mac Finder 
	- OR Open MySQL Workbench and use the Data Import tool and then run with MySQL Workbench).
	
	<img src="Images/data_import_1.png" width=200px><br><br>
	
	<img src="Images/data_import_2.png" width=500px>

4. Use Reverse Engineering in MySQL Workbench to create an ERD for the Chinook database you just installed. [Lesson Link](https://login.codingdojo.com/m/376/12528/88042)
	- Export the ERD into your repostory as a png file in your Data folder.
	- Insert the ERD into a Markdown cell in your notebook using the following syntax (make sure to change the filename to match yours, if you used something other tha "ERD.png")
	`![png](Data/ERD.png)`

5. In your Jupyter Notebook, use pymysql and sqlalchemy to connect your database. [Lesson Link](https://login.codingdojo.com/m/376/12528/89874)
```python
import pandas as pd
from sqlalchemy import create_engine
import pymysql
pymysql.install_as_MySQLdb()

## Change username and password to match your personal MySQL Server settings
username = 'root' # default username for MySQL db is root
password = 'YOUR_PASSWORD' # whatever password you chose during MySQL installation.

connection = f'mysql+pymysql://{username}:{password}@localhost/Chinook'
engine = create_engine(connection)
```
6. In your notebook, run the queries necessary to answer the following questions:
	
	1. What is the most a customer has spent on a single purchase and what is their customerId?
	2. What country is the customer with the largest total bill from (from question 2)?
	3. Which customerID made the fewest purchases (invoices)?
	4. Retrieve the id, name, and # of playlists for  tracks that appear on playlists more than 4 times?

7. Save your notebook and close Jupyter.
8. Save your work in a new commit with GitHub desktop and push it to GitHub.

	
