# InnScape 
*See it. Click it. Sorted.*

### Contents
* [Introduction](#Introduction)
* [Sentiment Analyses](#Sentiment-Analyses)
* [Topic Modeling](#Topic-Modeling)
* [Future Directions](#Future-Directions)
  * [Response Generation using OpenAI's GPT-2](#response-generation-using-openais-gpt-2)
* [Supplementary Information](#Supplementary-Information)

<br>

### Introduction

InnScape is a tool for hostel owners to obtain an overview of topics and sentiments from reviews, and select from suggested response templates to faciliate efficient responding. Engagement with customers have been found to increase the total number of reviews received by an establishment by 10%, and can also lead to rating improvements of up to 20%. Responding reasonably to negative reviews within 24 hours has also been shown to increase the likelihood of negative reviews being removed, or the reviewer looking upon the same establishment more favourably (*Trip Advisor, 2018*).

<br><br>
<p align="center">
  <img src="https://raw.githubusercontent.com/benjaminchew/InnScape/master/Figures/InnScape.png"></img>
</p>
<br><br>

### Sentiment Analyses

For appropriate assignment of pre-defined response templates to reviews, it can be helpful to have clear delineations between negative and positive sentiments. The following plot shows the distribution of sentiments scores for two popular sentiment analysis tools, VADER and TextBlob, across more than 100k reviews from Hostelworld. The x-axis ranges from -1 for the most negative sentiments to +1 for the most positive sentiments and the histogram colour indicates the actual hostel ratings. 

<br><br>
<p align="center">
  <img src="https://raw.githubusercontent.com/benjaminchew/InnScape/master/Figures/VaderBlob.png"></img>
</p>
<br><br>

While both VADER and TextBlob appear to capture the positive correlation between sentiments and ratings, VADER appears to be better suited for distinguishing between positive and negative sentiments here as the peaks across positive and negative ratings are further apart. 
<br>

### Topic Modeling

There are several popular approaches to topic modeling, one of which is Hierarchical Dirichlet Process (HDP) which is used when no *a priori* assumptions about the number of topics is known.

<br><br>
<p align="center">
  <img src="https://raw.githubusercontent.com/benjaminchew/InnScape/master/Figures/HDP.png"></img>
</p>
<br><br>

A useful tool for interactive topic visualization is [pyLDAvis](https://pypi.org/project/pyLDAvis/). From the figure above, it appears that the use of HDP on this review dataset produces 1 large cluster and multiple smaller ones that overlap a fair amount. This is not as useful for the purpose of producing concise and actionable insight, so I next turn to a semi-supervised form of MALLET Latent Dirichlet Allocation (LDA) using keywords extracted across labels from a small but annotated [TripAdvisor dataset](http://www.sobigdata.eu/dataset/tripadvisor-annotated-dataset).

A reasonable number of topics was determined by plotting and checking the coherence scores across different number of topics as seen in the plot below.

<br><br>
<p align="center">
  <img src="https://raw.githubusercontent.com/benjaminchew/InnScape/master/Figures/TopicNum.png"></img>
</p>
<br><br>

As the scores begin to plateau around 7 topics, I elected to use that number in the LDA for parsimony since each additional topic only contributed to an increase of approximately 0.01 in coherence. After converting the MALLET model to LDA using *malletmodel2ldamodel*, the topics can be visualized with pyLDAvis and we see that they are nicely segregated and non-overlapping. A caveat with topic models like this is that the label for topics are often open to interpretation and in this case, the topic in red appears to be relevant for "location" based on the topic keywords.

<br><br>
<p align="center">
  <img src="https://raw.githubusercontent.com/benjaminchew/InnScape/master/Figures/gLDA.png"></img>
</p>
<br><br>

The keywords for all 7 topics are listed below and can be loosely categorized under headings like *Cleanliness/Comfort*, *Service*, *Facilities*, *Others*, *Atmosphere*, *Value*, and *Location*

<br><br>
<p align="center">
  <img src="https://raw.githubusercontent.com/benjaminchew/InnScape/master/Figures/TopicBox.png" width="480"></img>
</p>
<br><br>

We can then parse each review through a sequence of pre-processing steps and extract the sentiments and topics at the sentence level. Since the output is continuous, we can define a threshold above or below which we categorize the sentiments as positive or negative. For the InnScape web app, it has been set to 0.4 and -0.4 respectively to increase the likelihood that the highlighting of sentences reflect the true sentiment associated with them. Sentences deemed to be neutral are not highlighted to reduce the need to attend to them. Depending on the overall sentiment of the review, pre-defined templates are suggested.

<br><br>
<p align="center">
  <img src="https://raw.githubusercontent.com/benjaminchew/InnScape/master/Figures/Response.png" width="800"></img>
</p>
<br><br>

### Future Directions

#### Response Generation using OpenAI's GPT-2

Moving away from pre-defined response templates, an exciting alternative would be to explore methods of generating "organic" responses. A potential avenue is OpenAI's Generative Pre-trained Transformer (GPT-2), a model that courted controversy when OpenAI refused to release its full version after deeming it too dangerous. Unlike traditional approaches that use Recurrent Neural Networks to capture sequential dependencies in sequences, the [Transformer](https://arxiv.org/abs/1706.03762) (*Vaswani et al, 2017; Figure below*) incorporates attention mechanisms.

<br><br>
<p align="center">
  <img src="https://raw.githubusercontent.com/benjaminchew/InnScape/master/Figures/Transformer.png" width="300"></img>
</p>
<br><br>

GPT-2 drops the encoder and is a 12-layer decoder-only transformer (*Radford et al, 2018; Figure below*). Containing a whopping 1.5 billion parameters, it has been trained to predict the next word using 40GB of internet text obtained from 8 million webpages. It applies a multi-headed self-attention operation over some input tokens, and the output distribution over target tokens is produced with a softmax following a series of position-wise feedforward layers. Supervised fine-tuning for specific tasks is done on top of the pre-trained model where the activation for the final transformer block is obtained and fed into an additional linear + softmax output layer.

<br><br>
<p align="center">
  <img src="https://raw.githubusercontent.com/benjaminchew/InnScape/master/Figures/GPT.png" width="680"></img>
</p>
<br><br>

After training a smaller version of GPT-2 (345M) on the responses of hostel owners to reviews, the model was able to generate sentences with reasonable levels of comprehension. For example, sample text generated in response to prompts like "service, admin, thanks" include:

* *"Thank you very much for your wonderful feedback. I really appreciate your comments and will forward your review to our staff as well."*
* *"Thank you so much for your wonderful feedback. We are sorry that you did not enjoy your roommates as much when staying with us."*
* *"Thank you for sharing your wonderful experience online! We are glad to read that you enjoyed your stay with us!"*

Not having had access to paid AWS Cloud Computing resources as a Data Science Fellow, training was performed using [Google Colab](https://colab.research.google.com/github/ak9250/gpt-2-colab/blob/master/GPT_2.ipynb) with minimally pre-processed data. The next round of pre-processing could involve Named-Entity Recognition (NER) to remove words that are less generalizable like establishment or worker names, or remove scenarios that are overly specific.
<br><br>
### Supplementary Information

#### Initial Approach

The original approach I had in mind when thinking about this project was to first find an annotated dataset that I could train a classifier on for the purpose of identifying topics within a review. If this goes well, then pre-processing new reviews and running them through this classifier sould be fairly straightforward.

<br><br>
<p align="center">
  <img src="https://raw.githubusercontent.com/benjaminchew/InnScape/master/Figures/OriginalApproach.png" width="680"></img>
</p>
<br><br>


There were approximately 500 reviews in the annotated dataset with sentences broken down across 11 labels, presenting a multi-label classification problem. The fact that many sentences were labeled 'Others' or 'Not Related' further reduces the number of actual samples that the classifer can be trained on for other labels such as 'Food' or 'Location'.

<br><br>
<p align="center">
  <img src="https://raw.githubusercontent.com/benjaminchew/InnScape/master/Figures/AnnotatedTA.png" width="450"></img>
</p>
<br><br>

With the undaunted enthusiasm of someone working on a Natural Language Processing (NLP) for the first time, I sought to reduce this imbalance by collapsing across similar labels and removing labels that were more relevant for hotels but not hostels. A snapshot of the final dataframe is shown below.



<br><br>
<p align="center">
  <img src="https://raw.githubusercontent.com/benjaminchew/InnScape/master/Figures/MLCData.png" width="650"></img>
</p>
<br><br>


Despite using models like Naive Bayes, Logistic Regression, Support Vector Classification, and Random Forest along with problem transformations like Binary Relevance, Classifier Chains, and Label Powersets, classifier accuracy was low with Naive Bayes + Label Powerset performing the best at 40% accuracy. 

<br><br>
<p align="center">
  <img src="https://raw.githubusercontent.com/benjaminchew/InnScape/master/Figures/FailedApproach.png" width="680"></img>
</p>
<br><br>

Having hit a snag at the first step of my initial approach, I next turned to topic modeling using Latent Dirichlet Approach as outlined in the main post.
