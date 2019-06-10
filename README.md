# InnScape
*See it. Click it. Sorted.*

[Topic Modeling] (###-Topic-Modeling)

InnScape is a tool for hostel owners to obtain an overview of topics and sentiments from reviews, and select from suggested response templates to faciliate efficient responding. 


<p align="center">
  <img src="https://raw.githubusercontent.com/benjaminchew/InnScape/master/Figures/InnScape.png"></img>
</p>


### Sentiment Analyses


For appropriate assignment of pre-defined response templates to reviews, it can be helpful to have clear delineations between negative and positive sentiments. The following plot shows the distribution of sentiments scores for two popular sentiment analysis tools, VADER and TextBlob, across more than 100k reviews from Hostelworld. The x-axis ranges from -1 for the most negative sentiments to +1 for the most positive sentiments and the histogram colour indicates the actual hostel ratings. 


<p align="center">
  <img src="https://raw.githubusercontent.com/benjaminchew/InnScape/master/Figures/VaderBlob.png"></img>
</p>


While both VADER and TextBlob appear to capture the positive correlation between sentiments and ratings, VADER appears to be better suited for distinguishing between positive and negative sentiments here as the peaks across positive and negative ratings are further apart. 


### Topic Modeling


There are several popular approaches to topic modeling, one of which is Hierarchical Dirichlet Process (HDP) which is used when no *a priori* assumptions about the number of topics is known.


<p align="center">
  <img src="https://raw.githubusercontent.com/benjaminchew/InnScape/master/Figures/HDP.png"></img>
</p>


A useful tool for interactive topic visualization is [pyLDAvis](https://pypi.org/project/pyLDAvis/). From the figure above, it appears that the use of HDP on this review dataset produces 1 large cluster and multiple smaller ones that overlap a fair amount. This is not as useful for the purpose of producing concise and actionable insight, so I next turn to a semi-supervised form of Latent Dirichlet Allocation (LDA) using keywords extracted across labels from a small but annotated [TripAdvisor dataset](http://www.sobigdata.eu/dataset/tripadvisor-annotated-dataset).

A reasonable number of topics was determined by plotting and checking the coherence scores across different number of topics as seen in the plot below.


<p align="center">
  <img src="https://raw.githubusercontent.com/benjaminchew/InnScape/master/Figures/TopicNum.png"></img>
</p>


As the scores begin to plateau around 7 topics, I selected to use that number for the LDA.


<p align="center">
  <img src="https://raw.githubusercontent.com/benjaminchew/InnScape/master/Figures/gLDA.png"></img>
</p>

