---
layout: post
title: Ghostwriter
permalink: posts/Markov
author: Daniel Huang
---
What word becomes shorter when you add two letters to it?

> Short.

## Intro 

Hello! Today we are going to be creating a simple language model that performs a task similar to something that you would ask ChatGPT - "Write like 'x' author"

Using tools like iteration and dictionaries, we will create a family of Markov language models for generating text. An n-th order Markov model is a function that constructs a string of text one letter at a
time, using only knowledge of the most recent letters. You can think of it as a writer with a "memory" of letters.

### 📚1

As always, lets import our libaries and modules! 

Note, s is our training text that comes from the first 10 chapters of Jane Austen's novel Emma. Intuitively, we are going to write a program that "writes like Jane Austen". 
For the purpose of cohesiveness, I have only put the beginning parts of the novel in this code snippet shown below, but the actual code runs on the entire 10 chapters. If you would like to follow along, please copy/paste the first 10 chapters of the book which you can find from the archives at Project Gutenberg.

``` python
import random

s = "CHAPTER I Emma Woodhouse, handsome, clever, and rich, with a comfortable home and..."
```

### 📚2

For us to properly train up our model, we need to first split up our training data into understandable parts. We will first get all the individual words, the move onto "n-grams", which *n* stands for the number of letters. 

For example, **bol** and **old** are the two 3-grams that occur in the string bold.

``` python
def count_characters(string):
  '''
  This function counts the number of times each character appears in a user-supplied 
  '''
    dictionary = {}
    mystring = string
    for i in range(len(mystring)): #iterates through the given string's length
    dictionary.update({mystring[i]:mystring.count(mystring[i])}) #for every index, 
    #s.count(s[i]) returns the count of a character

  return dictionary



def count_ngrams(string, n):
 '''
 This function counts the number of times a user-defined n-character sequence appear
 and creates a dictionary of n-character sequences based off the string 
 '''
    my_string = string
    dict = {}
    for i in range(len(my_string)): #iterates through the given string's length
    if i <= len(my_string)-n: #removes possibilities of undesired n-char sequences 
    if my_string[i:i+n] in dict: #if the char alr is in the dictionary, pass
    continue
    else:
    dict.update({my_string[i:i+n]:my_string.count(my_string[i:i+n])}) 
    #for every new char, updates the dictionaries' with its key and value
 
 return dict
```

Great! Now that we have created those two functions, lets give them a spin

```python
count_characters("tortoise")
count_ngrams("believe in me", n = 10)
```

> {'t': 2, 'o': 2, 'r': 1, 'i': 1, 's': 1, 'e': 1}

> {'believe in': 1, 'elieve in ': 1, 'lieve in m': 1, 'ieve in me': 1} 

Awesome, both functions work as expected. Something to note, for the ngrams example shown above, I used a 10-gram, but obviously you have the freedom to test with whatever gram and whatever sample text you want.

Alright, lets get cracking at this Markov Model, now that we have created some helpful baseline functions

``` python
def markov_text(s, n = 5, length = 100, seed = "Emma Woodhouse"):
  '''
  This function will take the following inputs:
  s -> given string from Jane Austen Book
  n -> given length of chars
  length -> how long the generated text will be
  seed -> the starting text in which the text will build off.
  '''
  my_text = seed #inialize output variable
  x = count_ngrams(s, n+1) #x stores the (n+1)-grams based off the n-gram
  t = list(x.items()) #t is a list of the dictionary returned from count_ngrams
  options = [] #for random
  weights = [] #for random
  matches = [] #initialize empty list to contain n+1 grams whose n chars match recent
  recent = seed[-n:] #initialize recent as the last n char of seed
    
  while len(my_text) <= length: #runs the loop till my_text reaches length
  
    for item in t: #iterates each tuple in t 
      if item[0][:n].__contains__(recent): #if the key in the tuple contains rece
        matches.append((item[0],item[1])) #add the tuple to matches
  
    for items in matches: #for each tuple in matches
      options.append(items[0]) #add the first element of the tuple to options
      weights.append(items[1]) #add the second element of the tuple to weights
  
    if weights == []: #if there are no matches for recent in s, reset recent to seed
      recent = seed[-n:]
      continue
  
    my_text += (((random.choices(options, weights))[-1])[-1]) #append last char
    options.clear() #clears option list for next iteration
    weights.clear() #clears weights list for next iteration
    matches.clear() #clears matches list for next iteration
    recent = my_text[-n:] #updates recent to new n-char sequence
    
    print (my_text)
```

### 📚3

Before we proceed, lets summarize what this function does, as it has a lot of moving parts!

Taking in a set of inputs, markov_text will attempt to complete a string with a set length (length) based off a seed word (seed) and the input text (s), using a certain limitation of reading characters at a time (n) to do so.

This is achieved through analyzing (n+1)-grams from the source text, where the frequency of these (n+1)-grams guides the selection of each subsequent character. The function employs a Markov chain approach, where the future state (next character) depends only on the current state (the most recent n characters). As a result, the output text is a probabilistically generated sequence that mimics the style and character patterns of the source text, while the seed ensures a specific starting point for the text generation process. 

Additionally, there is a failsafe such that if there are no (n+1) grams match the current *recent* n-char sequence, the function resets *recent* to the last n characters of the seed - allowing the function to continue generation in this circumstance.

### 📚4

Nice! Now that we have a clear picture on what exactly this function does, lets test it out!

```python
markov_text(s, n = 1, length = 50, seed = "Emma Woodhouse")
markov_text(s, n = 2, length = 50, seed = "Emma Woodhouse")
markov_text(s, n = 3, length = 50, seed = "Emma Woodhouse")
```

>Emma Woodhousey simared d. Fr Hawaven nepes hing a

>Emma Woodhouse's in in he his and his be perm bacc

>Emma Woodhouse, you know of marriet, whom to the i

Hmm, very interesting, it seems like the smaller the n-gram, the worse the performance of our model is. Lets test this out with a larger length to confirm our suspicions.

```python
for n in range (1,11) #note that we start at range 1!
  markov_text(s, n = n, length = 200, seed = "Emma Woodhouse")
  print ("\n")
```

> Emma Woodhousen Elelknompuns o hed cout id sharissevete w. not ipe aghoveld Bubjer int i
the Sm ped her henvingo s gh a ighelinn, d coonththic d anumor. y he coris, ank ssss 
ut sh sis. m s tut ofrt 

> Emma Woodhouself well!”—und am a like my fich hout quall ad, nothild's had, becif hets t
ook he ned day the is hich hopleforeake con der. Misty. Rom so thimand he he was eventio
n hink as grain al not S

> Emma Woodhouse, a mome should itselves say throw not restering make firstatisfield by at
of cred, a ver, but, the hout if you returns a givent! to becons overy well, can writed;
and my lad a ver think

> Emma Woodhouse away charade her manner: she, sured, that if you men may be and out he wa
y, Mr. Knight she my deal of such more undour me! but for having our satisfied and, as y
ou musiness.”“I have tha

> Emma Woodhouse's accepted the parlour-boarder. My only was to mission. At last time inde
ed, if you wrote her of what she is bring him, the air and a sister a longer will not li
ke his, came to as to be

> Emma Woodhouse, with a little education called for her. He could, and never boy, but a s
lave, May its approval beam in my father, or any thing of nothing which she count
ry to say that since I k

> Emma Woodhouse. Do help me. I never thing. After a little thing,” cried Emma had a very
good-nature, and the house for some warm prepossesses to be upon those for which nobody
hereabouts to attempted.

> Emma Woodhouse has given her a little in a lover.—“The expressions of the earth! their l
ittle raised on one side of this speeches which she could supposed; till they were not h
ere. I wish you may be a

> Emma Woodhouse told me. He did speak yesterday?”“Certainly, it is too young to settle. H
is mother and sisters, telling her to be forming themselves with a “very true; and it is
only one to please hers

> Emma Woodhouses were first in consequence of her caresses; and her place had been saying
so much against the scruples of his mother's, been the consequence of her sister at seve
nteen. She was not lost

Cool! It seems like our theory was reinforced by this test. Feel free to mess around with the inputs, or try different sources of text and copy your favorite author! Have a great day!

*Note - As n increases, the amount of text (the ngram) that is "matched" to the source text is increased, such that each pair has more characters, and less likely to stop in a sentence/word (b/c words [on average] are usually greater than 5 characters). More characters in the matching grams means that there is a higher chance that it will be grammaticaly correct (ex. the 2 gram 'us' n+1 gram's can be us., use, us , us?: a 6 gram 'house.' n+1 gram's would have house. , house. R, house. T, etc).*