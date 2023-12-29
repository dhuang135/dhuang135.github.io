---
layout: post
title: Applying Visualization to Real World Data
permalink: posts/Argonne
author: Daniel Huang
---
Why do programmers keep confusing Halloween and Christmas?

> Oct 31 == Dec 25

## Intro 

In the realm of data science, a well-crafted visualization not only simplifies the intricate patterns hidden in raw data but also tells a story that numbers alone cannot convey. This is the power of data visualization – transforming abstract numbers into a narrative that speaks volumes.

This document delves into a practical application of data visualization and Python in analyzing real-world data – specifically, the data pertaining to Summer Intern Participations at Argonne National Lab for the years 2022 and 2023. 

Note: Only output and code will be provided, as data used in analysis is private and contains PII. Snippets from 22' and 23' are mixed in. 

## Lets get into it! 

As a team coordinator at Argonne, I have a unique position in both operations and programming components of the various student internship programs hosted year-round.

It is of upmost importance to use data and make informed decisions to further improve our program, as well as identify what aspects of the program do and do not mesh well with students.

Using workday surveys, in addition to conversations with our students, we are able to paint a picture of the internship. 

## Using visualization and analysis to your advantage

The surveys sent out in Summer 22' and 23' had varying questions and different numbers of respondents, but in essence we are attempting to quantify different aspects of the internship experience for our students.

> So, where do our students come from? 

Plotly is a unique visualization package that has a lot of different modalities, and is especially useful when using maps and other forms of data visualization that extend beyond tables and graphs.

```python
import pandas as pd
import plotly.graph_objects as go
from plotly import express as px
from plotly.io import write_html

prim_df = pd.read_csv('statez.csv')
prim_df = prim_df.rename(columns={prim_df.columns[0]: "states" })
prim_df.head()

residence_df = prim_df[['state_2', 'value_2']]
residence_df = residence_df.sort_values('value_2', ascending=False)
residence_df = residence_df.reset_index(drop=True)
residence_table = residence_df.iloc[0:10,0:2]

fig = go.Figure(data=[go.Table(
    header=dict(values=["State","Number of Students"],
                fill_color='darkseagreen',
                align='center',
                font=dict(color='white', family="Lato", size=20)),
    cells=dict(values=[residence_table.state_2, residence_table.value_2],
               fill_color='lavender',
               align='left'))
])

fig.show()
```
{% include residence_table.html %}

```python
fig = px.choropleth(residence_df,  # Input Pandas DataFrame
                    locations="state_2",  # DataFrame column with locations
                    color="value_2", # DataFrame column with color values
                    color_continuous_scale="deep", # color scale
                    hover_name="state_2", # DataFrame column hover info
                    locationmode = 'USA-states', # Set to plot as US States
                    labels={
                     "value_2": "Students" })
fig.update_layout(
    geo_scope='usa',  # Plot only the USA instead of globe
)

fig.update_geos(
showsubunits=True, subunitcolor="black"
)

fig.update_layout(
    title={
        'text': 'What States Our Students Come From',
        'y':0.9,
        'x':0.5,
        'xanchor': 'center',
        'yanchor': 'top'})

fig.show()  # Output the plot to the screen
```

{% include residence_data.html %}

As noted above, we were able to visually map out where our students' were coming from to attend our internship. Albeit with a good amount of preprocessing, we were able to feed some simple data to our plotly constructors, and create some informative tables/graphs.

> Zooming into Illinois

Now that we understand where on the national scale our students our coming from, we want to get a deeper understanding of the distribution of local students (from IL).

For these visualizations, we will be using a unique identifier called a FIPS code (Federal Information Processing Series). State-level FIPS codes have two digits, county-level FIPS codes have five digits of which the first two are the FIPS code of the state to which the county belongs.

FIPS codes are dependent on the state, city, and zip code of a residence. Creating these Illinois by the Counties visualizations required using the US Census Bureau and [conversion databases](https://www.kaggle.com/datasets/danofer/zipcodes-county-fips-crosswalk), and taking our indivdidual data and processing them through the databases.

```python
import pandas as pd
import plotly.express as px
import numpy as np
import plotly.figure_factory as ff
from plotly.io import write_html

#22' 
prim_df = pd.read_csv('countydata.csv')
unique_values = prim_df['FIPS'].value_counts()
unique_values = unique_values.to_frame().reset_index() 

values = unique_values["FIPS"].tolist()
fips = unique_values["index"].tolist()

test = np.arange(16999,17204, 2)
fips_IL = test[1:]

numbers_IL = np.full_like(fips_IL, 0)

for i in range(len(fips)): 
    index = np.where(fips_IL == fips[i])[0][0]
    numbers_IL[index] = values[i]

fippy = fips_IL.tolist()
valuey = numbers_IL.tolist()

colorscale = ["#f7fcfd","#bfd3e6","#9ebcda","#8c96c6","#8c6bb1","#88419d","#810f7c","#4d004b"]


fig = ff.create_choropleth(fips= fippy, values= valuey, 
                         scope = ['Illinois'], show_state_data=True,
                           colorscale = colorscale,
                           simplify_county=0, simplify_state=0,
                           state_outline={'color': 'rgb(100,15,15)','width': 1},
                           title = "Illinois by Counties",
                           asp = 2.6,
                          county_outline={'color': 'rgb(15, 15, 15)', 'width': 0.5})

fig.update_layout(
    autosize=False,
    width=1250,
    height=600,)

fig.update_layout(legend=dict(
    yanchor="top",
    y=0.99,
    xanchor="left",
    x=0.20,
    title = "Number of Students",
    bgcolor="white",
    bordercolor="Black",
    borderwidth=2
))


fig.layout.template = None
fig.show()
```
{% include illinois_countydata2022.html %}

```python
#23'
fips_df = pd.read_csv('2023fipsdata.txt')
values = fips_df["Counts"].tolist()
fips = fips_df["FIPS"].tolist()

IL_fips = np.arange(16999,17204, 2)
fips_IL = IL_fips[1:]
fips_IL

numbers_IL = np.full_like(fips_IL, 0)

for i in range(len(fips)): 
    index = np.where(fips_IL == fips[i])[0][0]
    numbers_IL[index] = values[i]
 
fippy = fips_IL.tolist()
valuey = numbers_IL.tolist()

colorscale = ["#ffffff", "#0056fe", "#006ffe", "#0085fe", "#009bf8", "#00b0ec", "#00c4db", "#00ddc6", "#00f693", "#00ff1e"]


fig = ff.create_choropleth(fips= fippy, values= valuey, 
                         scope = ['Illinois'], show_state_data=True,
                           colorscale = colorscale,
                           simplify_county=0, simplify_state=0,
                           state_outline={'color': 'rgb(100,15,15)','width': 1},
                           title = "Illinois by Counties",
                           asp = 2.6,
                          county_outline={'color': 'rgb(100, 15, 15)', 'width': 0.5})

fig.update_layout(
    autosize=False,
    width=1800,
    height=900,)

fig.update_layout(legend=dict(
    yanchor="top",
    y=0.99,
    xanchor="left",
    x=0.20,
    title = "Number of Students",
    bgcolor="white",
    bordercolor="Black",
    borderwidth=2
))

#fig.update_layout(showlegend=False)

fig.layout.template = None
fig.show()
```

{% include illinois_countydata2023.html %}

In the code above, we are utilizing figurefactory's create_choropleth function to create these visualizations that break down number of students per county in Illinois. 

Using numpy, we create an array of all the counties located in Illinois, and create a secondary (dummy) array that corresponds to the number of students in each county. These two arrays are what we send to the create_choropleth function.

> So, how do our students feel about their mentors?

```python
# loading in all the essentials for data manipulation
import pandas as pd
import numpy as np
import string
from nltk.corpus import stopwords
from nltk import ngrams
# We can use counter to count the objects
from collections import Counter
# visual library
import seaborn as sns
import matplotlib.pyplot as plt
#bubble plot
import circlify

#set up data
mentor = pd.read_table('mentorPostExpSummer2023.txt')
stop_words = set(stopwords.words('english'))

mentor_comments = " ".join(mentor['Please reflect on your mentor/supervisor’s willingness and ability to support your questions and work as an intern, as well as their receptiveness to ideas.'])
mentor_comments = mentor_comments.split()
filtered_words = [mentor_comments for mentor_comments in mentor_comments if mentor_comments.lower() not in stop_words]
filtered_text = " ".join(filtered_words)
punctuations = set(string.punctuation)
no_punc = str.maketrans("","", string.punctuation)
filtered_text = filtered_text.translate(no_punc)
filtered_text = filtered_text.lower()
#at this point, filtered_text is just all the text w/o punc or stopwords.

#get counts, create dataframe
freq_dict = Counter(filtered_text.split())
freq_dict = dict(freq_dict)
sorted_words = sorted(freq_dict.items(), key=lambda x:x[1], reverse=True) #cheeky one-liner
top_words = pd.DataFrame(sorted_words[:75])
top_words.columns = ['word', 'count'] #quick little name change

plt.figure(figsize = (21, 10))
plt.xticks(rotation=0)  # Adjust the rotation angle as needed

sns.barplot(
    x = "count", 
    y = "word", 
    data = top_words.head(15),
    palette = "Paired"
)

plt.tight_layout()
plt.show()
```

![](/images/mentorwords.PNG)

In the code above, we did the following steps:
1. Set up data
2. Cleaned the data using stopwords and other NLP data-cleaning basics
3. Create a new dataframe of our cleaned words, and visualize!

We can extend this process to analyzing 2-grams of words/feedback that students had given.

```python
counted_2 = Counter(ngrams(filtered_text.split(),2)) #count all the pairs in the filtered_text string
word_pairs = pd.DataFrame(counted_2.items(),columns=['pairs','frequency']).sort_values(by='frequency',ascending=False) #create dataframe
#word_pairs

plt.figure(figsize = (21, 10))
plt.xticks(rotation=0)  # Adjust the rotation angle as needed

sns.barplot(
    x = "frequency", 
    y = "pairs", 
    data = word_pairs.head(15),
    palette = "Paired"
)

plt.tight_layout()
plt.savefig('mentorpairs.png', dpi=900, facecolor='w')

plt.show()
```

![](/images/mentorpairs.PNG)

Looking at these visualizations, it lets us quickly parse through the hundreds of responses we got about how students felt about their mentors, and what their experience being mentored by a staff scientist was like. This lets us understand the general sentiment of our cohorts.

> So, where do our students want to go after Argonne?

Utilizing the process that I created above to parse through the free response questions for Mentorship, I can also apply different free response questions to the same process and come out with the same visualizations but for different questions.

Below's output is exactly that. 

![](/images/planspairs.PNG)

This informs us of what kinds of plans students in our cohort are looking to pursue, as we gave students the ability to freely respond instead of be restricted to multiple choice. Even so, how helpful is this histogram? Are there better ways to show this data?

We can extend the data to show in different modalities, below is code made to output what the histogram looks like in a more visual format of bubbles.

```python
top_pairs_circles=word_pairs[0:29] #we are just taking a subset of a new question's data "what is your plan after this intership" that was processed using the same data cleaning process as before

circles = circlify.circlify(
    top_pairs_circles['frequency'].tolist(), 
    show_enclosure=False, 
    target_enclosure=circlify.Circle(x=0, y=0, r=1)
)

# Create just a figure and only one subplot
fig, ax = plt.subplots(figsize=(15,15))

# X axes
ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)
ax.spines['bottom'].set_visible(True)
ax.spines['left'].set_visible(False)
ax.tick_params(axis='y', which='both', left=False, right=False, labelleft=False)

# Find axis boundaries
lim = max(
    max(
        abs(circle.x) + circle.r,
        abs(circle.y) + circle.r,
    )
    for circle in circles
)

#add/subtracting so it can fit the enlarged circles
plt.xlim(-lim-3, lim+3)
plt.ylim(-lim-3, lim+3)

# list of labels
labels = top_pairs_circles['pairs'][::-1] #[::-1] flips the order of the list, such that the label and circle matches

for i, (circle, label) in enumerate(zip(circles, labels)):
    x, y, r = circle
    
    # Multiply x, y, and r values to make the circles bigger
    x = x * 4  # Example multiplication factor of 2 for x
    y = y * 4  # Example multiplication factor of 2 for y
    r = r * 4  # Example multiplication factor of 2 for r
    
    color = "green" if i % 3 == 0 else ("#0068FF" if i % 3 == 1 else "red")
    ax.add_patch(plt.Circle((x, y), r*.99, alpha=.2, linewidth=1, color=color))
    plt.annotate(label, (x, y), va='center', ha='center', size=int(r/(4*.018))+4.5)
    #make sure to divide r by the multiplier so the text stays the same ratio

    
ax.set_aspect('equal')    
plt.tight_layout()
plt.savefig('toppairsafterinternship.png', dpi=1200, facecolor='w')
```

![](/images/toppairsafterinternship.PNG)

What do these pairs mean? Well, the size of the circle tells us how 'popular' that word pair exists within the source data. Therefore, looking left to right on the visualization, we can see that most students are planning to go back to school to finish their degree or pursue a graduate degree.

## Conclusion
If you want to learn more in-depth on the strategy and process to analyzing these datasets, feel free to hop over to my portfolio 😊.