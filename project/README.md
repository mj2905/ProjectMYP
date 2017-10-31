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

