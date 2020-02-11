CodeBook for the FinalCSV and anim_template_data_preprocessing
=============================

Data source
-----------
The dataset can be found on the project_visuallanguage chat in slack: https://creativitylab-ucsd.slack.com/files/UTBHQ8XSB/FTH2C2CP2/finalcsv.xlsx

Feature Selection 
-----------------

Refer to the last section of CodeBook for how the data was manipulated and sorted. 

I refer you to the last section of this markdown and the original dataset to learn more about the feature selection for this dataset. And there you will find the follow description:

The dataset contains the following Field Names:
Channel	video id	|__videoKind	|__publishedAt	|__videoTitle	|__playlistId	|__categoryId	|__duration	|__viewCount	|__likeCount	|__disLikeCount	score	participant id	# of votes	mean	std

The data examined, is the compilation of over 20,000 survey responses conducted online by UCSD Professor Haijun Xia. This survey's goal is to collect the masses' opinion on a collection of infographic videos. Such opinions will aid in the filtering of possible "animation templates" where we can identify animations compiled within the many youtube videos online.

Focus on the field: __video id__. 

The features of this dataset revolve around video ids. Each video id has a tag for being a youtube video(videoKind), date of publish(publishedAt), title (videoTitle),  it's playlist (playlistID), the number of categories associated or could be associated __(categoryId, the key for the categoryIds can be found here https://gist.github.com/dgp/1b24bf2961521bd75d6c)__ , the total time of the video (duration), the amount of views (viewCount), the amount of likes (likeCount), and the amount of dislikes (disLikeCount).

We've also established our own Fields revolving around the results of the survey. A collection of 0's,1's,2's, and 3's that represent scores(score), a collection of participants that gave said scores (participant id), a counter for the amount of scores given per id (# of votes), the mean of ths scores(mean), and the standard deviation of the scores(std).

How these parameters for the dataset came to be was based on what Professor Haijun Xia believed offer the most value in identifying possible animation template videos. 

Understanding the CSV
-----------------

__Channel__:UCfdNM3NAhaBOXCafH7krzrA | The Infographics Show

__videoId__: 7dBQZj1yaFc

__videoKind__: youtube#video

__publishedAt__: 2017-12-21T17:00:00.000Z

__videoTitle__: What Would Happen If All Humans Went Extinct?

__playlistID__: UUfdNM3NAhaBOXCafH7krzrA

__categoryId__: 27

__duration__: PT4M40S (Play time 4 min and 40 seconds)

__viewCount__:797149

__likeCount__:17327

__disLikeCount__:504

__score__:0, 2, 2

__participant id__: ['ALHT37PYQA05D', 'A1YFETJK9CSLGP', 'A2HX13XTRKDD9C']

__# of responses__: 3

__mean__:1.333333333

__std__:0.942809042

The functions used for arithmetic calculations of scores
--------------------------------------------------------

* mean(): Mean value
* std(): Standard deviation

How was the data extracted?
---------------------------

Utilizing Python's ability to manipulate csv and xlsx files, we utilized a library called Pandas.
For arithmetic calculations, we used the Python library NumPy.

NumPy = A library of numerical computations. 

Pandas = A library for data wrangling and data manipulation. 

```python
#Imported libraries and extensions
import numpy as np
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
from numpy import *
import json
from IPython.display import display
import re
```

```python
df = pd.read_excel('FinalCSV.xlsx') #read xlsx file and save as df
df.to_csv('videoData.csv', index=False) #convert df to csv and save it, set index to false to prevent saving row index as additional column
df = pd.read_csv('videoData.csv') # read csv as df
df # display entire dataframe(manipulated csv file contents)
```
```python
dfFirstRow = list(df.iloc[0])# first row of data frame 
colName = list(df.columns.values) # column name values

regex = re.compile('[^a-zA-Z]') # use regex to only extract alphabetic characters
firstCharToLower = lambda test_str: test_str[:1].lower() + test_str[1:] if test_str else '' #create lambda function to convert first char to lowercase

for i, word in enumerate(colName): #for each word in of the ith index in the list colName
        word = regex.sub('', colName[i]) # remove all non-alphabetic char
        word = firstCharToLower(word) #convert first char to lowercase
        colName[i] = word #replace colName

# more pre-processing to clean up colName
for i, word in enumerate(colName):
    if "publishedAt" in colName[i]:
            colName[i]= 'publishedDateTime'
    if "videoid" in colName[i]:
            colName[i]= 'videoId'
    if "disLikeCount" in colName[i]:
            colName[i]= 'dislikeCount'
    if "ofvotes" in colName[i]:
            colName[i]= 'numOfVotes'
    if "participantid" in colName[i]:
            colName[i]= 'participantId'

df.columns = (colName)# rename colName
```

```python
df.shape #10573 videos with 16 columns
#stores the number of rows and columns as a tuple (number of rows, number of columns)
```

```python
#df[df['mean'] == 0]['mean'].shape  #also returns num of videos with mean score of 0
df[df['mean'] ==0]['mean'].count()
```

```python
df[df.duplicated(['videoId'])]['videoId'].count() #number of videos with duplicates: 790
df[df.duplicated(['videoTitle'], keep = False)][['videoTitle', 'videoId']] # videoTitle and videoId of all duplicates based on videoTitle
#obtaining number of duplicates and finding all duplicates based on videoId
```

```python
#drop is a pandas function that deletes a row or column of data, with controllable parameters
# dropping ALL video duplicates based on videoId by keeping first one
df.drop_duplicates(subset ="videoId", keep = "first").count()
```

```python
meanFreqDict = df['mean'].value_counts().to_dict()
#meanFreqDict
sortedMeanFreqByMean = {}
for key in sorted(meanFreqDict):
    sortedMeanFreqByMean[key]=  meanFreqDict[key]
    
sortedMeanFreqByMean # dictionary sorted by Mean Score as keys

len(sortedMeanFreqByMean) #63 mean scores as keys,

```

```python
meanScoreDF = pd.DataFrame(lists)
normalizedMeanScore=(meanScoreDF[meanScoreDF.columns[0]]-meanScoreDF[meanScoreDF.columns[0]].min())/(meanScoreDF[meanScoreDF.columns[0]].max()-meanScoreDF[meanScoreDF.columns[0]].min())
#normalizedMeanScore

normalizedMeanScoreDF  = pd.DataFrame(lists)
# normalizedMeanScoreDF.columns[0]
normalizedMeanScoreDF[0] = normalizedMeanScore
normalizedMeanScoreDF
#Data obtained here for use in Matlab plotting 
```



