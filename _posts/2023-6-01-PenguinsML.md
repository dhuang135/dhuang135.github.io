---
layout: post
title: Machine Learning Penguins
permalink: posts/Penguins
author: Daniel Huang
---
What is a penguin's favourite type of lettuce?

> Iceberg!

## Intro 

Hello! 

Today, we will be evaluating the Palmer Penguins data set collected by Dr. Kristen Gorman and the Palmer Station,
Antarctica LTER, a member of the Long Term Ecological Research Network. 

Using visual and mathmatical analysis alongside machine learning models, we can create a predictive model that will be able to evaluate the species of a penguin based off limited information.


Outlined are the following steps that we will be taking on our computational analysis journey!
1. Importing Data and Modules
2. Exploratory Analysis
3. Cleaning and Splitting of Data
4. Modeling of Machine Learning
5. Visualization and Testing of Machine Learning Models

### 🐧Importing Data and Modules🐧

As always, we must import the libraries and modules neccessary for visualization and analysis.

The following imports will help to produce and troubleshoot our programs. While you may not see some of them used right now, its imperative to import everything into your environment when you start to keep things organized and avoid bugs!

``` python
import matplotlib.pyplot as plt
import random
import numpy as np
import pandas as pd
import seaborn as sns
from sklearn.model_selection import cross_val_score
from sklearn import tree, preprocessing
from sklearn.model_selection import train_test_split
from sklearn.neural_network import MLPClassifier
from sklearn.linear_model import LogisticRegression
from sklearn.svm import SVC
from warnings import simplefilter
from sklearn.exceptions import ConvergenceWarning

#read in data
url = 'https://philchodrow.github.io/PIC16A/datasets/palmer_penguins.csv'
penguins = pd.read_csv("palmer_penguins.csv")

#taking a sneak-peek at the data
penguins.head(3)
```
   *Looking at the first few rows of the dataframe allows us to take a quick look at what type of data we are working with.*

| Variable Name | Description | 
| :---         |   :----  |  
| 'studyName'        | Name of study       | 
| 'Sample Number'      | Sample number        | 
| 'Species Region'     | Region where species originates from        | 
| 'Island'    | Island where sample lives       | 
| 'Stage'     | Stage of life of sample        | 
| 'Individual ID'     | ID of sample        | 
| 'Clutch Completion'     | Y/N        | 
| 'Date Egg'     | Date of egg        | 
| 'Culmen Length (mm)'     | Measure of culmen        | 
| 'Culmen Depth (mm)'    | Measure of culmen        | 
| 'Flipper Length (mm)'     | Measure of flipper       | 
| 'Body Mass (g)'    | Mass measure        | 
| 'Sex'    | M/F       | 
| 'Delta 15 N (o/oo)'     | Measure of isotope       | 
| 'Delta 13 C (o/oo)'     | Measure of isotope       | 
| 'Comments'     | Extra comments        | 

### 🐧Importing Data and Modules🐧
### 🐧Exploratory Analysis🐧
### 🐧Cleaning and Splitting of Data🐧
### 🐧Modeling of Machine Learning🐧
### 🐧Visualization and Testing of Machine Learning Models🐧