# Books versus eBooks : The customer's choice

## Milestone 1

### Abstract
A 150 word description of the project idea, goals, dataset used. What story you would like to tell and why? What's the motivation behind your project?

----

There has been a lot of discussions about the difference between books and ebooks, some arguing that the former is better than the latter [https://devicebar.com/articles/ebooks-vs-books-pros-and-cons/2041/], as it may be easier to focus on it, and the feeling when reading a physical copy is better. However, there are some additions like interactivity, hyperlinks and functionalities, inherent to the ebook format, that may have a positive impact on readers. Thus, it's legitimate to ask ourselves whether people, for the same content, tend to prefer the virtual or physical format, if we base our reasoning on real data, and not on subjective reasons. For this study, we will use the Amazon database, in which we do have reviews and grades for different books and ebooks.

### Research questions
A list of research questions you would like to address during the project. 

----

For the same content, which format seems to be prefered by people, based on Amazon reviews ?

What is the price difference between the two supports, globally and per book category ?

Per region, what is the favorite format between virtual and physical ?

### Dataset
List the dataset(s) you want to use, and some ideas on how do you expect to get, manage, process and enrich it/them. Show us you've read the docs and some examples, and you've a clear idea on what to expect. Discuss data size and format if relevant.

----

We want to use the Amazon database, especially the Book and Kindle Store parts. We have seen that there are metadatas that we will be able to use, to merge the two databases using the title fields (with some cleaning). Furthermore, we do have some reviews and ratings, which we will be using to determine our metric. Of course, if we were only using the metric, it would potentially imply some bias. We will try to lower it using for example the "helpful" field. Also, for the per region analysis, we will be using links http://www.amazon.com/gp/cdp/member-reviews/*user_id* with *user_id* being the user id of the review, so that we can retrieve the reviewer location. We will also see if the number of reviewers is significant enough, otherwise it would be biased as the notation criteria would not be the same for everyone, and it would not be softened by the amount of reviews.



### A list of internal milestones up until project milestone 2
Add here a sketch of your planning for the next project milestone.

----

We need to explore the data, to see what is possible, and if our assumptions were right :

We will thus need to learn how to use the cluster as it will be taught in the next exercise session, and as there is a big amount of data available.

Then, we will have to clean the titles, to merge books with ebooks, and see if there are multiple sellers, and how we will handle them (depending on their number, we might merge these "duplicates").

We will also see how it's possible to extract the region data from the reviewers using URLs (or in a simpler way if we find some), using the cluster or our computers.

See if we can use some kind of machine learning algorithm to infer the country (or at least continent) for reviewers for which it's not specified, depending on the timestamp, the language used and maybe the name.

Use some vizualisation techniques to show our results : for the region specific question, it will depend on the reviewers location spreading. If all of them are in the US, it would be more US specific, in a per state manner. Otherwise we will work with countries and regions.

### Questions for TAs
Add here some questions you have for us, in general or project-specific.

----

## Milestone 2

### Downloading the DataSet :

We accessed the cluster using ssh@iccluster060.iccluster.epfl.ch, then using 
```shell
hadoop fs -get /datasets/productGraph/metadata.json /buffer
```
to move the dataset to a folder we could connect to with SCP to download it on our computer.

We also went to this <a href='http://jmcauley.ucsd.edu/data/amazon/'>link</a>, and we downloaded the Books and Kindle Store (ebooks) 5-core files.

### Installing NLTK

We will need NLTK to analyse the text of the reviews, more precisely we will use the package Vader.

To install NLTK `pip install nltk` 

Then in a notebook, run `nltk.download()` or `nltk.download_shell()` if the first command didn't work. Then type `d` and `vader_lexicon` to download the necessary files for Vader to work.

### Set up Spark

1) Install a local copy of Spark

```Sh
wget http://mirror.switch.ch/mirror/apache/dist/spark/spark-1.6.3/spark-1.6.3-bin-hadoop2.6.tgz
tar -zxvf spark-1.6.3-bin-hadoop2.6.tgz
```

2) Download the YARN configuration for the ADA cluster

YARN is the resources manager introduced with Hadoop 2.0. It takes care of the scalability of your Spark jobs and optimizes the usage of the cluster.
[Download the archive](ADA_YARN.zip) with the configuration, and unpack it in a local directory.

3) Set the env variables YARN_CONF_DIR SPARK_HOME and HADOOP_USER_NAME, and add spark to your path.

```sh
export SPARK_HOME=<spark_directory>
export PATH=$SPARK_HOME/bin:$PATH
export YARN_CONF_DIR=<YARN_config_directory>
export HADOOP_USER_NAME=username
```

Where *<YARN_config_directory>* is the full path of the directory where you have the configuration files and *username* is your Gaspar Account.

Note: this is temporary. If you want to avoid to type this setup every time you reboot you computer, update your shell config (i.e. $HOME/.bashrc)

4) Create a new file called *spark-defaults.conf* in the config directory of Spark *(<spark_directory>/conf/spark-defaults.conf)* and add the the following lines:

```sh
spark.driver.extraJavaOptions -Dhdp.version=2.7.3
spark.yarn.am.extraJavaOptions -Dhdp.version=2.7.3
```

This specifies the version of Hadoop that the ADA cluster is using.

5) Install *findspark* for the integration with Jupiter Notebook

```sh
pip install findspark
```

6) Launch *pyspark* with optional parameters to save resources

```sh
pyspark --master yarn
pyspark --master yarn --executor-memory 2G --executor-cores 5 --num-executors 4
```

7) Add the following line in your Jupyter Notebook

```python
import findspark
findspark.init()
import pyspark
sc = pyspark.SparkContext(appName="Pi")
from pyspark.sql import SQLContext
sqlContext = SQLContext(sc)
```

And if you don't need spark but still need to run the notebook, it may be good to run

```python
sc.stop()
```



### Reading the DataSet

Since the metadata file was too big (10 Go, for 9430088 entries), we created a function to read it line by line.
Using this function we filtered the file by categories to get one file with the metadata for the books, and one for the metadata of the ebooks.  

### Merging the books and ebooks

In order to compare the books and ebooks, we need to be able to merge them. However we don't have any column of our data that allows us to do a proper merge. The title for the ebooks is only given for 44 of the entries.  

From there we tried many differents things. All we could really use was the ASIN of the ebooks that we could easily get from the Kindle Store 5-core reviews. So we tried scrapping Amazon for the ebooks information. But it turns out that amazon has a pretty efficient anti-bot system, and our program was thus quickly told that it was doing something improperly. We tried some tricks by changing the user agent but this would result in Amazon telling us to stop scrapping and use the API.

To use the API, we would have to register using a credit card, so we decided to try finding an alternative. We have also applied for a student account, but as even the day before the request, we are still waiting for the epfl approval.

Since merging from the metadata alone is not possible, we had to find something else.
And we did find a third-party website, http://asindb.com/, that allows us to get at least the title for some ebooks given the ASIN. Using this, we couldn't get all the data, but we managed to get part of it.  

With some ebook titles available to us, we then applied a merge on those titles with the book dataset (with different tricks to get the most matchings). However we quickly found out that almost all the matches we got were duplicates, with only 148 uniques matches.

More importantly, we also found that most of the unique matches were for books and ebooks that don't have the same content.
So we would need to also get the author of the books in order to have a better match, but we cannot obtain this information from http://asindb.com/, so we are back to square one.

We did try to use other services like the library genesis or Gutenberg, but unfortunately we cannot get a book from its ASIN.

So we are still looking for a solution. If we manage to get this amazon AWS student account, we will use it, otherwise we might think about using our credit card if really needed.

### Review analysis

In parallel to our merging issues, we computed the scores for each books and ebooks. We use two approaches:

1) A weighted average of the stars taking into account the helpfulness of the review as described below.

For each pair of book's review, we will compute a weight that is the number of positive votes for the review divided by the total number of votes for this review. If the review has no vote, we'll set the weight to 0,5.

Then we will define a weighted rating average of the book, that will be defines as the sum of the product of the review's rating and its weight, which we will divide by the sum of all weigths (the formulas are given in the notebook).

2) A weighted average of the sentiment's intensity in the review taking into account the helpfulness with the weight being derived similarly as above.

Here we are going to use the VADER (Valence Aware Dictionary sEntiment Reasoner) sentiment analyzer from the nltk package. VADER is based on lexicons of sentiment-related words and each words is rated as whether it is positive and negative, and how negative or positive it is. For example, the 'excellent' would be treated as more positive than 'good'.

Although Vader is not the most accurate tool and to analyse a piece of text it checks if any of the words in the text are present in the lexicon, therefore its accuracy depends on the coverage of the lexicons. It is the easiest approach we have for the moment as we cannot train a classifier since we don't have a proper training set.

For the sentiment, we actually average the score of the review summary and main text.

3) What we do with it

With those two scores (helpfulness and sentiment), we compute the mean of the sentiment for a given rating (1 star, 2 stars, ...) and we found that they increase accordingly (more positives reviews as the rating increases).  

Then comparing the sentiment score for a given rating between books and ebooks, we observe that the books tends to have more positive reviews, at all levels of rating. This could be explained by the fact that people reading physical books would be more likely to write complete reviews.

But let's keep in mind that we are working on a very limited dataset (~50 entries) that we know is incorrect (same title for different content), so this analysis is just a showcase of our pipeline, and the results in the end might end up being very different.

### To answer the questions for milestone 2

1) We can handle the data in its size : we already filtered it to get what we are interested into. For the metadatas and 5-core files, we have created smaller files containing what we want, and it runs pretty much quickly (except the first time, when we set the booleans at the beginning of the notebook to true). There is just one bottleneck : retrieving the title (and other informations like the author). The algorithm used to retrieve the title information took 2.5 hours to request 4000 asins. As there are around 60000 ebooks asin to retrieve, it can take a long time (with the actual configuration).
2) We understand what is into the data : We know now that the title information for ebooks and author information for all are missing. Thus, we need to retrieve those.
3) We considered ways to enrich, filter, transform the data according to your needs : yes we do, because we need more details on each entries than what is given.  

For all our analysis, we still need to think about data retrieving, as we have seen some issues with it.
However, if we use incorrect data to simulate what we would get with good data, we already have some analysis pipeline for the main question.

For the price analysis, we will use the same merged data that we need for the review analysis. So we have the same problem as for the review analysis.

But for the region analysis, we are not sure we will be able to get the regional data, since its already hard to get the author without Amazon AWS.
The same argument might be applied to the question proposed in milestone 1, about analysing the length of books and ebooks to see the public preference.

If we quickly figure out a solution for this problem, we will work on them.


## Milestone 3

In addition to libraries mentioned previously, we have used also bottlenose, which is a small library to obtain from here : https://github.com/lionheart/bottlenose, by using the setup.py file.

For the contributions of all group members :

Marc has worked on analysing the data, scraping the data (code and retrieve it), the analysis of the number of pages, the sales rank and the scores, and the report.

Yves has worked on the scraping of data (code and retrieve it), the interpretation of the analysis, the report and will do the final presentation.

Pierre-Alexandre has worked on the scraping of data (retrieving it), and a lot on data analysis about sentiment and score, also on the report.

For the report, it's called ada2017.pdf : https://github.com/mj2905/ProjectMYP/blob/master/project/report/ada2017.pdf
