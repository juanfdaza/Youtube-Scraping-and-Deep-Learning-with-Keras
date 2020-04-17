# Find the most attractive words for a podcast title

**Juan Fernández-Daza**

**[Part-TimeData Analytics Bootcamp --  Madrid -- Apr19]**

## Overview

The objective of this project is to assess an owner of Podcast channel in Youtube, to choose the most impactful titles and get the highest amounts of views or likes.
Once every two weeks a new podcast is delivered to the community, and its clear that words used on the titles can make the users to be more attracted.

Notes:

A different number of skills, tools and techonologies have been used, such as:
	* Python.
	* Jupyter Notebook.
	* Statistical analysis.
	* Data visualization -- via Plotly.
	* Google API.
	* Google Sheets.
	* Deep Neural Network.
	* Word Embedding.
	* JSON and csv output files.
	* Web scraping -- youtube.
	* Selenium -- open G Chrome and scroll down automatically
	* Stackoverflow

## Projects workflow

The project consists on 4 python notebooks to obtain the main KPIs of a group of episodes of a podcast called "Pero entonces..." plus on a separate process the KPIs for its parent channel "Phi Beta Podcast". Once there, then write down all the "Pero Entonces" podcasts' KPIs into a GSheets doc and finally, evaluate the relation between the title of each podcast and the number of likes/views.

Step by step:

####  1a. Jupyter notebook #1a -- Scraping_from_youtube.ipynb

* Scrape each podcast of the child channel's "Pero Entonces" KPIs and write them down in different JSON files.

* Techniques & Python libraries used: BeautifulSoup (scraping), urllib (to run the requests), json (writer of the files)
* Output: a JSON file by podcast title with its main KPIs. Example:

	{
	    "TITLE": "Pero Entonces x01 | Cómo ser influencer",
	    "PUBLICATION_DATE": "2019-09-27",
	    "NUMBER_OF_VIEWS": "2802 visualizaciones",
	    "LIKES": "69",
	    "DISLIKES": "7",
	    "URL": "https://www.youtube.com/watch?v=l1CjJ4UJo-Q&list=PLeZfGBk92nGBjxFoHCIdllJN-jj0tAZd7&index=2&t=0s"
	}

####  1b. Jupyter notebook #1b -- Scraping_from_youtube-others.ipynb

* Scrape each parent channel "Phi Beta Podcast" podcasts' KPIs and write them down in csv file.

* Techniques & Python libraries used: BeautifulSoup (scraping), urllib (to run the requests), Selenium (scrape all the page scrolling down to get more episodes)
* Output: a csv file by podcast title with its main KPIs.

#### 2. Jupyter notebook #2 -- Gsheets-update.ipynb

* Read JSON files and create a dataset
* Establish the API connection and open the GSheets file
* Write all the new data in each tab of the Workbook -- again only from the "Pero Entonces" episodes, its the focus. 
* The logic is as follows: a) Its a new episode -- a new tab is created with a column of the KPIs of the day; b) The episode already existed -- a new column is added with the KPIs of the day (See photo "Gsheets_tab_example"). Instructions have been taken mainly from [1].
* Update "Summary" tab that works as a DB with the records of each day -- a daily evolution can be done.
* Plot a) Daily evolution of the likes and views and b) The last picture (last day) --  in both cases, individually per episode and of the whole channel. 
* Write the Summary tab into a csv. 

* Techniques & Python libraries used: Google API, pygsheets (modify the GSheets doc from the jupyter notebook), plotly (data visualization).
* Output: a csv file with the Summary tab to use it in the model of the 3rd notebook.

#### 3. Jupyter notebook #3 -- Likes_and_views_estimator.ipynb

* In this case, two csv files are read (1 from focus "Pero Entonces" and 2 the rest of subchannels of "Phi Beta Podcasts"), next, the data is adapted to fit in the model, and the last step is to run the Deep Learning model.

* a) Data loading

* The initial contains these columns -- with these types of data.

	Name                object
	Date        datetime64[ns]
	Views                int64
	Likes                int64
	Dislikes             int64
	Link                object
	Pub_date    datetime64[ns]
	Episode             object
	dtype: object

* Basically it contains the title of each episode and the KPIs per day.

* b) Data Wrangling and Cleaning

* Some columns are completely useless for the model, the following columns are eliminated: Date, Pub_date, Episode and Link.
* A new column is calculated with the # of days since the publication.

* c) Data Analysis

* Boxplots and scatter matrix are drawn to evaluate the distribution of each KPI -- likes, views, dislikes and number_of_days_since_publication. These graphs show some outliers that may confuse the model in likes and views (see "Boxplot distribution the episodes per KPI" and "KPI matrix")


* d) Model Training and Evaluation

* The article [2] has been key to run this model. Two main tasks take place:

1. Deep Neural Network
* The whole algorith is run with KERAS. KERAS libraries directly tokenize the words and pads them. This way the X_train and X_Test are created.
* Regarding the model, its a Sequential() model which apart from receiving the X and delivering y, it uses inputs such as vocab_size, embedding_dimension.
* Output: the user introduces a new title and the algorith returns a value between 0 and 1, the higher the more effective the title will be.

2. Word2vec embedding
* In this case, the library gensim includes a model which builds a "dictionary" of values that are including in the training dataset. It allows the user to include a word and find similarity with other words. This library also tokenizes each title and its used to plug it directly in the KERAS model -- in the part where the Embedding function is called.

* Techniques & Python libraries used: KERAS, Word2vec (models to work with words), gensim (library that contains the models Word2Vec), Wordcloud plot.
* Output: a coefficient that estimates the effectiveness on likes and views that a new title has.


## Conclusion

* The words used in each episode title are definitely linked to the number of views or likes that video will have.
* KERAS and gensim are ideal to build models that are based on texts. They help tokenize and prepare the data to run different ML or DL models.
* The final correlation between words and likes/views is not sufficiently robust to extract great conclusions. The reason is that the dictionary constructed by scraping all these Episode titles (around 1k) is quite small, no more that 1200 words.

## Next steps

* Definitely run the model when the Youtube channel includes abruptly the number of episodes.
* Also, an additional timeseries ML code would be useful to estimate the likes/views in a particular period of time

## References
* [1] How to Automate Google Sheets with Python, Dayal Chand Aichara Jun19, https://medium.com/game-of-data/play-with-google-spreadsheets-with-python-301dd4ee36eb
* [2] Machine Learning -- Word Embedding & Sentiment Clasiffication using Keras, JavaidNabi Oct18, https://towardsdatascience.com/machine-learning-word-embedding-sentiment-classification-using-keras-b83c28087456
* Plotly tutorials -- https://plotly.com/python/
* Towards data science -- https://towardsdatascience.com/
* Stackoverflow as a encyclopedia -- https://stackoverflow.com/