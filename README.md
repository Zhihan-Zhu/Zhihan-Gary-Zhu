# Data Science Portfolio

- [Data Science Portfolio](#data-science-portfolio)
  - [Data Analysis and Visualization](#data-analysis-and-visualization)
      - [San Francisco City Data Analysis](#san-francisco-city-data-analysis)
  - [Naural Language Processing](#naural-language-processing)
      - [Open-Source Python package steam-review-scraper](#open-source-python-package-steam-review-scraper)
      - [Web Scraping and Sentiment Analysis for Steam reviews of No Man's Sky](#web-scraping-and-sentiment-analysis-for-steam-reviews-of-no-mans-sky)
      - [Popular topics in mainstream media](#popular-topics-in-mainstream-media)
  - [Machine Learning](#machine-learning)
      - [Home Credit Default Risk Challenge](#home-credit-default-risk-challenge)
  - [Time Series](#time-series)
      - [Nintendo Stock Price Analysis](#nintendo-stock-price-analysis)
  - [Statistical Test](#statistical-test)
      - [NBA Player Salary Analysis](#nba-player-salary-analysis)

This is a portfolio containing my data science projects for academic, self-learning purpose.

They are written in either Python or R.

## Data Analysis and Visualization
#### San Francisco City Data Analysis 
([Jupyter](https://nbviewer.jupyter.org/github/Zhihan-Zhu/Zhihan-Gary-Zhu/blob/master/SF%20Data/SF%20Data%20Analysis.ipynb))

In this notebook, I look deep into an SQLite database that contains data of crime, parking, schools, housing, etc., and gain insights of housing choices from various maps using Matplotlib Basemap toolkit.

## Naural Language Processing

#### Open-Source Python package steam-review-scraper 
([PyPI page](https://pypi.org/project/steam-review-scraper/); [Github page](https://github.com/Zhihan-Zhu/steam-review-scraper))

Be inspired by the project of [Steam review analysis on Game No Man's Sky](#web-scraping-and-sentiment-analysis-for-steam-reviews-of-no-mans-sky) I did previously, I decided to wrap up a package that can download reviews of any game from Steam given the game id. Therefore, this package has the following functionalities:
- get game id from a given game name
- get a list of n game ids
- get the total number of reviews for a game
- get all reviews of a game including attributes such as review content, review post date, recommendation, helpfulness.

#### Web Scraping and Sentiment Analysis for Steam reviews of No Man's Sky 
(Jupyter: [Data Collection](https://github.com/Zhihan-Zhu/Zhihan-Gary-Zhu/blob/master/No%20Man's%20Sky/No%20Man's%20Sky%20data%20collection.ipynb); [Analysis](https://github.com/Zhihan-Zhu/Zhihan-Gary-Zhu/blob/master/No%20Man's%20Sky/No%20Man's%20Sky%20Analysis.ipynb))

*No Man's Sky* got massive negative reviews at its launch time because of its failure to meet the features it had promised to players. The situation has changed after several big updates. This notebook collects all the english steam reviews of this indie game, and explores how this game improves. Finally, a model is trained for sentiment analysis.

#### Popular topics in mainstream media 
([Jupyter Code](https://github.com/Zhihan-Zhu/Zhihan-Gary-Zhu/blob/master/Trending%20articles/finalproject.ipynb); [presentation](https://github.com/Zhihan-Zhu/Zhihan-Gary-Zhu/blob/master/Trending%20articles/index.md))

What are the main topics of mainstream meidia from different countries for a same time period? Do they focus on the same news or not? This notebook collects over 5,000 articles from ***The New York Times*** and ***The Guardian*** for the last week of Feb, 2012 using APIs and BeautifulSoup, and tries to find trending topics and similar topics with NLP techniques.

## Machine Learning
#### Home Credit Default Risk Challenge 
([Jupyter](https://nbviewer.jupyter.org/github/Zhihan-Zhu/Zhihan-Gary-Zhu/blob/master/Home%20Credit%20Default%20Risk/Home%20Credit%20Risk.ipynb))

This problem is a former competition on Kaggle that predicts the applicants' capability of repaying loans. In this notebook, I am not compete. I read all the great kernels in this competition and tried to learn from those top competitors about how to gain insights from massive data and do feature engineering.

## Time Series
#### Nintendo Stock Price Analysis 
([R Markdown](http://htmlpreview.github.io/?https://github.com/Zhihan-Zhu/Zhihan-Gary-Zhu/blob/master/Nintendo%20Stock/Final_Project.html))

Time series analysis using ARIMA, POMP, GARCH models to log return of Nintendo Stock Price.

## Statistical Test
#### NBA Player Salary Analysis 
([R Markdown](https://github.com/Zhihan-Zhu/Zhihan-Gary-Zhu/blob/master/NBA%20Salary/NBA%20Salary.md))

In this project I use the the 2017-2018 season players's salaries and stats of regular seaseon data to explore the most important factors that affect players salaries with a linear regression model. I use mocked data and Monte Carlo approach to choose between nonparametric bootstrap and parametric bootstrap based on their power of Hypothesis Test that whether coefficients equal to zero, and use the preferred one to create confidence interval for the coefficients.
