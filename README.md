# InnScape
*See it. Click it. Sorted.*

InnScape is a tool for hostel owners to obtain an overview of topics and sentiments from reviews, and select from suggested response templates to faciliate efficient responding. 

<p align="center">
  <img src="https://raw.githubusercontent.com/benjaminchew/InnScape/master/Figures/InnScape.png"></img>
</p>

### Sentiment Analyses

For appropriate assignment of pre-defined response templates to reviews, it can be helpful to have clear delineations between negative and positive sentiments. The following plot shows the distribution of sentiments scores for two popular sentiment analysis tools, VADER and TextBlob, across more than 100k reviews from Hostelworld. The x-axis ranges from -1 for the most negative sentiments to +1 for the most positive sentiments and the histogram colour indicates the actual hostel ratings. While both VADER and TextBlob appear to capture the positive correlation between sentiments and ratings, VADER is better suited for distinguishing between positive and negative sentiments here as the peaks across positive and negative ratings are further apart. 

<p align="center">
  <img src="https://raw.githubusercontent.com/benjaminchew/InnScape/master/Figures/VaderBlob.png"></img>
</p>

### Topic Modeling

There are several popular approaches to topic modeling, one of which is Hierarchical Dirichlet Process (HDP) that can be used when no a priori assumptions about the number of topics is known.

<p align="center">
  <img src="https://raw.githubusercontent.com/benjaminchew/InnScape/master/Figures/HDP.png"></img>
</p>
