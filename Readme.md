Intreduction

This project is a knowledge-base for word-prediction

system, based on Google 3-Gram dataset, using Amazon Elastic Map-Reduce

(EMR). The produced knowledge-base indicates the probability of each
word trigram

found in the corpus.

Probability Function

We used held out method, named deleted estimation.

Held out estimators divide the training data (the corpus) into two
parts, build initial

estimates by doing counts on one part, and then use the other pool of
held out data to

refine those estimates.

The deleted estimation method, for instance, uses a form of two-way
cross validation,

as follows:

![A picture containing diagram Description automatically
generated](./images/media/image1.png){width="4.0459558180227475in"
height="0.7698556430446194in"}

$${\ ▪\ N\ is\ the\ number\ of\ n - gram\ instances\ in\ the\ whole\ corpus.
}{\ ▪\ Nr_{0}\ is\ the\ number\ of\ n - gram\ types\ occurring\ r\ times\ in\ the\ first\ part\ of\ the\ corpus.\ 
}{▪\ Tr_{01}\ is\ the\ total\ number\ the\ n - grams\ of\ the\ first\ part\ \left( of\ Nr_{0}\  \right)appear\ the\ second\ part\ of\ the\ corpus\ (instances).\ 
}{▪\ Nr_{1}\ is\ the\ number\ of\ n - gram\ types\ occurring\ r\ times\ in\ the\ second\ part\ of\ the\ corpus.\ 
}{▪\ Tr_{10}\ is\ the\ total\ number\ the\ n - grams\ of\ the\ second\ part\ (of\ Nr_{1}\ )\ appear\ in\ the\ first\ part\ of\ the\ corpus\ (instance).}$$

Map-Reduce:

First map-reduce:

Count the occurences in each corpus for each 3-gram.

input:

Key: \<lineId\> , Value : \<3-gram\>

output:

Key: \<3-gram\> , Value : \<count in corpus 0 , count in corpus 1\>

Second map-reduce:

Calculates the probability for each r.

Input:

Key: \<3-gram\> , Value : \<count in corpus 0 , count in corpus 1\>

Output:

key: \<3-gram,probability\> , value : \<probability\>

Adding probability to key for map-reduce3 sorting

Mapper:

Creates 3 new rows from each row in first map-reuce output:

1\. calculation of N_r0, Tr_01 for the current 3-gram

2\. caluculation of N_r1, Tr10 for the current 3-gram

3\. the total count of the current 3-gram in both corpuses

Combiner:

Combine localy only N_r0,N_r1,Tr_01,Tr10 values.

Probability cacluation can accur only by the reducer after we got
N_r0,N_r1,Tr_01,Tr10 values from all computers.

Reducer:

Key sorting implemntation: sort by r value and if r value matched sort
by boolean value.

Use r and false tag to calculate all N_r0,N_r1,T_r01,Tr10 values for the
same r

Use r and true tag to create each 3-gram with total count r probabilty
with N_r0,N_r1,Tr_01,Tr10 final values

Third map-reduce:

Used only for the final sorting,

\<w1,w2\> ascending and probability descending with shuffle and sort
from mapper to reducer

Input:

Key: \<3-gram,probability\> , Value : \<probability\>

Output:

Key: \<3-gram,probability\> , Value : \<probability\>

run instructions:

create a bucket and upload to it the heb-stopwords.txt included and
change file type to utf8

in buckeat create empty log folder

in main.jav:

lines 21,23,27,29,33,35,76 change bucket name

in mapperreducer1.java

line 70 change bucket name

package the code and upload to all parts jar files to bucket
