# Introduction

This project is a knowledge-base for word-prediction
system, based on Google 3-Gram dataset, using Amazon Elastic Map-Reduce (EMR). The produced knowledge-base indicates the probability of each word trigram found in the corpus.

# Probability Function

We used held out method, named deleted estimation.

Held out estimators divide the training data (the corpus) into two
parts, build initial estimates by doing counts on one part, and then use the other pool of held out data to refine those estimates.

The deleted estimation equation:

![A picture containing diagram Description automatically
generated](./images/media/image1.png)

Where:

N is the number of n-gram instances in the whole corpus.

Nr_0 is the number of n-gram types occurring r times in the first part of the corpus.

Tr_01 is the total number the n-grams of the first part (of Nr_0 )appear the second part of the corpus (instances).

Nr_1 is the number of n-gram types occurring r times in the second part of the corpus.

Tr_10 is the total number the n-grams of the second part (of Nr_1 )appear in the first part of the corpus (instance).

# Map-Reduce:

### First map-reduce:

Count the occurrences in each corpus for each 3-gram.

Input:
`Key: <lineId> , Value : <3-gram>`

Output:
`Key: <3-gram> , Value : <count in corpus 0 , count in corpus 1>
`

#####  Mapper:

Gets the 3-gram from each line, generates a new key and increments the corresponding counter to 1 for the selected corpus


##### Reducer:

For each key the reducer sums up the counters of each corpus

##### Combiner:
Same as reducer

### Second map-reduce:

Calculates the probability for each r.

Input:
`Key: <3-gram> , Value : <count in corpus 0 , count in corpus 1>
`

Output:
`key: <3-gram, probability> , value : <probability>
`

Adding probability to key for map-reduce3 sorting

#####  Mapper:

Creates 3 new rows from each row in first map-reuce output:

1\. calculation of N_r0, Tr_01 for the current 3-gram

2\. calculation of N_r1, Tr10 for the current 3-gram

3\. the total count of the current 3-gram in both corpuses

##### Combiner:

Combine locally only N_r0,N_r1,Tr_01,Tr10 values.

Probability calculation can occur only by the reducer after we got
N_r0,N_r1,Tr_01,Tr10 values from all computers.

##### Reducer:

Key sorting implementation: sort by r value and if r value matched sort
by Boolean value.

Use r and false tag to calculate all N_r0,N_r1,T_r01,Tr10 values for the
same r

Use r and true tag to create each 3-gram with total count r probability
with N_r0,N_r1,Tr_01,Tr10 final values

### Third map-reduce:

Used only for the final sorting:

w1,w2 ascending and probability descending with shuffle and sort
from mapper to reducer

Input:
`Key:  <3-gram, probability> , Value :  <probability>
`

Output:
`Key:  <3-gram,probability> , Value :  <probability>
`

# Run instructions:

Create a bucket and upload to it the heb-stopwords.txt included and
change file type to UTF-8.

In bucket create empty log folder

In main.java:
Change bucket name in lines 21,23,27,29,33,35,76 

In mapperreducer1.java:
Change bucket name in line 70 .
Package the code and upload to all parts jar files to bucket.
