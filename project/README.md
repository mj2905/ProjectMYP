# Books versus eBooks : The customer's choice

# Abstract
A 150 word description of the project idea, goals, dataset used. What story you would like to tell and why? What's the motivation behind your project?

----

There has been a lot of discussions about the difference between books and ebooks, some arguing that the former is better than the latter [https://devicebar.com/articles/ebooks-vs-books-pros-and-cons/2041/], as it may be easier to focus on it, and the feeling when reading a physical copy is better. However, there are some additions like interactivity, hyperlinks and functionalities, inherent to the ebook format, that may have a positive impact on readers. Thus, it's legitimate to ask ourselves whether people, for the same content, tend to prefer the virtual or physical format, if we base our reasoning on real data, and not on subjective reasons. For this study, we will use the Amazon database, in which we do have reviews and grades for different books and ebooks.

# Research questions
A list of research questions you would like to address during the project. 

----

For the same content, which format seems to be prefered by people, based on Amazon reviews ?

What is the price difference between the two supports, globally and per book category ?

Per region, what is the favorite format between virtual and physical ?

# Dataset
List the dataset(s) you want to use, and some ideas on how do you expect to get, manage, process and enrich it/them. Show us you've read the docs and some examples, and you've a clear idea on what to expect. Discuss data size and format if relevant.

----

We want to use the Amazon database, especially the Book and Kindle Store parts. We have seen that there are metadatas that we will be able to use, to merge the two databases using the title fields (with some cleaning). Furthermore, we do have some reviews and ratings, which we will be using to determine our metric. Of course, if we were only using the metric, it would potentially imply some bias. We will try to lower it using for example the "helpful" field. Also, for the per region analysis, we will be using links http://www.amazon.com/gp/cdp/member-reviews/*user_id* with *user_id* being the user id of the review, so that we can retrieve the reviewer location. We will also see if the number of reviewers is significant enough, otherwise it would be biased as the notation criteria would not be the same for everyone, and it would not be softened by the amount of reviews.



# A list of internal milestones up until project milestone 2
Add here a sketch of your planning for the next project milestone.

----

We need to explore the data, to see what is possible, and if our assumptions were right :

We will thus need to learn how to use the cluster as it will be taught in the next exercise session, and as there is a big amount of data available.

Then, we will have to clean the titles, to merge books with ebooks, and see if there are multiple sellers, and how we will handle them (depending on their number, we might merge these "duplicates").

We will also see how it's possible to extract the region data from the reviewers using URLs (or in a simpler way if we find some), using the cluster or our computers.

See if we can use some kind of machine learning algorithm to infer the country (or at least continent) for reviewers for which it's not specified, depending on the timestamp, the language used and maybe the name.

Use some vizualisation techniques to show our results : for the region specific question, it will depend on the reviewers location spreading. If all of them are in the US, it would be more US specific, in a per state manner. Otherwise we will work with countries and regions.

# Questions for TAs
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

### Reading the DataSet

Since the metadata file was too big (10 Go, for 9430088 entries), we created a function to read it line by line.
Using this function we filtered the file by categories to get one file with the metadata for the books, and one for the metadata of the ebooks.  

One of the first problems was that some ebooks were in the same category as regular books, but since we will later merge the two, this was not a real problem.

### Merging the books and ebooks

In order to compare the books and ebooks, we need to be able to merge them. However we don't have any column of our data that allows us to do a proper merge. The title for the ebooks is only given for 44 of the entries.  

From there we tried many differents things. All we could really use was the ASIN of the ebooks that we could easily get from the Kindle Store 5-core reviews. So we tried scrapping Amazon for the ebooks information. But it turns out that amazon has a pretty efficient anti-bot system, and our programm was thus really ineficient at scraping. We tried some tricks by changing the user agent but this would result in Amazon telling us to stop scrapping and use the API.

To use the API, we would have to register using a credit card. So because of this and because we found out so close to the deadline, we decided for the moment not to use it. We applied for a student account, but as I am writing we are still waiting for the approval of our request.

Since merging from the metadata alone is not possible, we had to find something else.
And we did find a third-party website, http://asindb.com/, that allows us to get at least the title of our ebook given the ASIN. Using this, we couldn't get all the data, but we managed to get most of it.  

With most of the ebooks titles available to us, we then applied a merging on those titles with the book dataset (with different tricks to get the most matchings). However we quickly found out that almost all the matches we got were duplicates, with only 148 uniques matches.

More importantly, we also found that some of the matches were for books and ebooks that didn't have the same content.
So we would need to also get the author of the books in order to have a better match, but this we cannot obtain from http://asindb.com/, so back to square 1.

So we are still looking for a solution to this. If we manage to get this amazon AWS student account, we will be good.

### Reviews analysis

In parallel to our merging issues, we are trying find a way to get the sentiment from the individual reviews, using a library named "nltk". We also trying for get a weight of the review according to its 'helpful" score.


### To answer the basic questions for milestone 2

1) We can handle the data in its size : we already filtered it to get what we are interested into.  
2) We understand what is into the data : we certainly know what is missing in the data and what we will use in it.  
3) We considered ways to enrich, filter, transform the data according to your needs : yes we are because we need more details  on each entries than what is given.  

Our new plan is now to find a way to get all the metadata we needl, which we hope to be able to do using the amazon AWS student account we applied for.

Our final objective haven't changed, but we realized many obstacle are on our path, and we are finding solutions for them.


