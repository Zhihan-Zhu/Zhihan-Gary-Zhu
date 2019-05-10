<a href="/Zhihan-Gary-Zhu">Home</a>

# Mr./ Trump/ Said 
## The analysis of trending words in the last week of February
##  Yuren Zhang, Zhihan Zhu 
The complete code is provided <a href="https://github.com/littlegaga/STA141B/blob/master/finalproject.ipynb">here</a>
### 1. Why Do We Care?
Trending words are good indicators of the current status. Higher word frequencies are positively correlated to more coverage and exposure. In other words, more people are talking or searching about this term. Also, trending words in a period of time efficiently described heat topics and important news during that period. 
To find out what are the **current trending**  words (in the last week of February), my partner and I looked at two mainstream media: The New York Times (NYT) in the U.S. and the Guardians in the U.K. Using two sources from different countries lead to smaller bias. In addition, comparison between sources are also made avaliable. We found that the trending words from the two media varies significantly.
### 2.  Methods 
__1. For NYT:__
 NYT provides its API for public use. Thus, we utilize this advantage and acquired the links for most viewed articles from all sections. Most viewed articles represent what topics do the audiences cars most about. So they are good indicators of the current focusing points of NYT's audience as well as reporters. We choose not to download all articles within the month because of the limitation of non-commercial license as well as poor efficiency. 
The code we used for utilizing the API may or may not be useful to you:
```python
 url="https://api.nytimes.com/svc/mostpopular/v2/mostviewed/all-sections/30/?api-key=YourKey"
 response=requests.get(url)
 urllist=[response.json()[u'results'][i][u'url'] for i in range(len(response.json()[u'results']))]
```
Then, those links are used to access individual articles. The content of individual articles are acquired by web-scrapping. 
Again the code we used for web-scrapping as well as text stripping may or may not be useful to you:
```python
def getcontent(url):
    """
    Extract the title, text, and author of an Aggie article
    Input: url-> url for the article. 
    Output: a dictionary that contains the author, text, url and title.       
    """
    artic= requests.get(url,"lxml")
    soup_arti=BeautifulSoup(artic.text)
    content=soup_arti.select("article[id='story'] > div[class='story-body-supplemental'] > div > p ")
    if content==[]:
        content=soup_arti.select("p[class='paragraph--story']")
    text=" ".join([i.text.strip() for i in content])
    return text
```
Text are stripped and analyzed after removing stopwords using the built-in function from Word Cloud. The result is presented in Word Cloud form.

__The results:__

<img src="https://github.com/zyrr95/Final-Project-Sta-141B/blob/master/nyt.png?raw=true">

The size of each word is correlated to its relative frequency in the whole text. The results showed that in the last week of February, Mr. Trump, the President clearly took the majority attention from NYT reporters and audiences. In addition, his opinions and statements are wildly reported as the word 'said' appears a lot.
The U.S. is clearly focusing on Mr. Trump. However, across the Atlantic, people do not necessarily care about the same.


__2. For the Guardian__

The Guardian doesn't have a API method of most viewed articles that is similar to NYT, so we choose to obtained all the articles of the last week of Februrary. With similar skills of web-scrapping, text analysis, we get the following Word-Cloud:

Since the API does not allow us to directly get the urls of articles. We first write a function to find urls:
```python
def url_perpage(page,pagesize='50',fromdate='2017-02-22',todate='2017-02-28',form='json',order='oldest',key='YourKey'):
    url='https://content.guardianapis.com/search'
    response=requests.get(url,params={'page':page,'page-size':pagesize,'from-date':fromdate,'to-date':todate,'format':form,'order-by':order,'api-key':key})
    return [x['webUrl'] for x in response.json()['response']['results']]

urllist=[]
for page in range(1,37):
    urllist=urllist+url_perpage(page)
```

then write another function to get the article content given an url:
```python
def getcontent_Guardian(url):
    """
    Extract the title, text, and author of an Aggie article
    Input: url-> url for the article. 
    Output: a dictionary that contains the author, text, url and title.       
    """
    artic= requests.get(url)
    soup=BeautifulSoup(artic.text)
    try:
        text=" ".join([i.text.strip() for i in soup.find_all('div',itemprop='articleBody')[0].find_all('p')])
        text=text.translate({ 0x2018:0x27, 0x2019:0x27, 0x201C:0x22, 0x201D:0x22, 0x2026:0x20 })
    except (IndexError,TypeError):
        text=''
    return text
```

__The results:__

<img src="https://github.com/zyrr95/Final-Project-Sta-141B/blob/master/guardian.png?raw=true">

Although we still can see Trump on that picture, it is not the most popular thing that British people care. From those words such as "year", "new" and "time", we can have a guess that the Guardian focuses on the future, the plan of the country since February was still the beginning of 2017.



__3. similar articles__

The two most similar articles from these two medias are __“I Ignored Trump News for a week. Here’s what I Learned”__ from NYT and __“At last, here are ‘White House’ tweets that offer hope”__ from the Guardian. The first one talks about the phenomenon that Trump is everywhere on social medias, which exactly reflects the Word-Cloud. The second one talks about an twitter account that purports to leak information of the president. So we can see that the intersection between the trends in this week was also President Trump.

### 3. Limitations

We acknowledge that the analysis is a bit plain and thin; however, due to certain limitations, our project cannot go any further. Because of poor computer performance as well as API rate limitation for non-comericial license, we campare the most popular content from NYT with all the articles from the Guardian, which might be inappropriate. For the same reason, we only investigae on one week.
Therefore, if we are authorized to pull information at a higher rate and get access to higher computational power (such as using Amazon Web Service), for the next step we want to comparing the contents from the two medias of a whole month (with almost 10,000 articles each). 
This could 
<ol>
<li>increasing accuracy.</li>
<li>potentially implementing an analysis of word frequency change against time.</li>
</ol>
We believe with the relatively larger dataset and avaliable time data my partner and I would find more attributes to analyze and better content to present. 