# The Joy of Machine Learning
_Predicting viewership of Bob Ross’ The Joy of Painting episodes on YouTube using painting elements_

#### 1. Problem & Client

- The social media manager for Bob Ross’ official website wants to run a
competition for Bob Ross ‘paint-a-likes’. Contestants (‘Bob-testants’?) will
film themselves painting in the same style, and submit videos for judging.

- To maximize the potential for visibility of this project, she wants to know
what painting topics predict the greatest viewership of existing Bob Ross
episodes hosted on YouTube. These elements will be required in
contestants’ submitted painting videos.

#### 2. Data Acquisition
2.1 PAINTING ELEMENTS
Fortunately, someone has already sifted through all The Joy of Painting
episodes and listed elements present in the final painting.  These elements include trees, mountains, fog, clouds, rivers....  These data are hosted on the github for FiveThirtyEight, https://github.com/fivethirtyeight/data/tree/master/bob-ross. The painting categorizations data was already very clean, but I removed categories with less than 5 elements, and then made sure categories were relevant (e.g. removed tags like ‘guest host’ or describing the painting frame).

2.2 EPISODE STATISTICS
I also scraped video information from the YouTube channel using the YouTube Data API  (https://developers.google.com/youtube/v3/getting-started), and then read through the resulting json text for information like episode title and view count.

2.3 JOIN
Finally, I performed a join on these two tables, dropping any episodes that weren’t contained in the elements dataset, and removed rows with missing data in viewership.

#### 3. Data exploration
Here is a summary of elements in all the paintings in the final dataset.

![element histo](https://raw.githubusercontent.com/mskaerthomason/Springboard-Capstone_Bob-Ross/master/figs/painting_histo.png)

Unsurprisingly, trees (in their various forms) are the most common element in Bob Ross' paintings. He is, afterall, known for his 'happy trees'.  Closely following are clouds, mountains, bodies of water, and grasses.

It was also important to define when an episode hosted on YouTube was 'viral'.  These data may (or may not) be affected by YouTube's own algorithms, but for the sake of this exercise, I am going to assume that effect is negligible. In this histogram below, there is a fairly clear split in the data around 900,000 views, so I chose this as the 'viral' threshold.

![view histo](https://raw.githubusercontent.com/mskaerthomason/Springboard-Capstone_Bob-Ross/master/figs/views_hist.png)

#### 4. Model development
4.1 Feature design
Painting elements were already designed in a binary fashion (0 or 1), with a column for each element.  The viral threshold was used to create a binary target variable for classification.

4.2 Model process
Two model families were chosen for comparison, a decision tree classifier and a random forest ensemble classifier. Seventy percent of the data were used for training the models, and models were tuned and compared using k-fold cross validation.  

An important issue to mention here is that the target categories were very unbalanced.  Viral videos were only ~10% of the group, so achieving an effective predictor (e.g. a model that would actually predict true positives) is quite challenging. Given this challenge, I optimized models using recall (measures the number of true positives relative to the rest of the predictions) and area under the ROC (takes into account both precision and recall).

#### 5. Results
Both families of models were, as expected, not performing well at predicting true positives in the test data (actual viral videos).  Most models had none (recall = 0), and AUC-ROC between .55 and .60.  The best overall model predicted one true positive.

In this best model, the top five important features determining viral-ness were `clouds`, `fog`, `river`, `lake`, and `conifer`.

#### 6. Conclusions & Next steps

So my current recommendation for Bob Ross, Inc.'s social media manager would be to take a union of the most common elements, as well as the most important elements from the random forest model.  

##### Elements to require in contestant videos:
- trees (especially conifer)
- clouds and/or fog
- mountains
- river and/or lake

##### Strategies for improvement
- Clean the painting elements further: remove duplicate categories (tree, trees, conifer...) or group commonly co-occurring elements
- Re-think the problem: rather than classify videos as viral (or not), predict the number of views a video will receive
