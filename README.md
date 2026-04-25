# How does US Media Perceive the World? New York Times Front-Page Coverage Analysis  
## Country Mentions, Article Volume, and Topic Modeling from 1980 to 2024

---

## Introduction

For over a century, U.S. news coverage has served as one of the main ways American audiences understand both foreign and domestic events. Although there has been extensive research on media bias, less attention has been given to how the frequency and framing of country coverage change over time.

The main goal of this research is to identify long-term trends in political narratives in *The New York Times* and uncover hidden patterns that may be difficult to notice because of the rapid pace of article publication. The findings suggest that international representation in U.S. news involves two connected dynamics: which countries receive coverage and how those countries are framed for readers.

This study contributes to media research by showing that changes in front-page narratives over time are important for understanding how *The New York Times* presents politics and global events to its audience.

---

## Data and Method

The data for this project was collected from the *New York Times* Archive API. Articles were retrieved by month and year, then filtered to include only front-page articles. Front-page placement was used because it signals editorial importance and reflects which stories were considered most significant at the time.

After collection, the data was cleaned and organized by date and article metadata. The analysis included exploratory data analysis, country mention counts, topic modeling, and data visualization across time.

### Data Summary

| Component | Description |
|---|---|
| Source | *New York Times* Archive API |
| Retrieval Method | Articles collected by month and year |
| Filter | Front-page articles only |
| Rationale | Front-page placement signals editorial importance |
| Processing | Cleaned and organized by date and metadata |
| Analysis Methods | Topic modeling, country mention counts, and data visualization |

---

# Exploratory Data Analysis

## Articles per Year

### Process Explanation

The first part of the analysis examined how many front-page articles appeared each year from 1980 to 2024. The code reads each selected decade CSV file and counts how many articles were published in each year. It first loads the file using pandas, converts the `pub_date` column into a date format, and extracts the year from each publication date.

The main function used to count the yearly article totals is `value_counts()`. In this code, `df['year'].value_counts()` counts how many times each year appears in the `year` column, which represents the number of articles published in each year. Then, `sort_index()` organizes the results in chronological order from earliest to latest.

The final output is stored in a dictionary called `articles_per_year_per_decade`, where each decade file name, such as `1990_1999`, is connected to its yearly article counts. After the yearly counts were calculated, the data was exported to Excel and visualized in Power BI.

### Result Interpretation
By categorizing for readability, we were able to identify sub-topics and main topics, which help us see focus trends across times. 

![countries-mentions](https://github.com/tu-tong/NYT-Front-page-Coverage-Analysis/raw/main/Initial%20Results/countries-mention.png)


The chart shows that the number of front-page articles increased from the 1980s into the early 2000s, reaching its highest point around 2006. This peak may be connected to major global and domestic events during that period, including the Iraq War, North Korea’s nuclear test, U.S. midterm elections, and terrorism-related coverage.

After 2008, the number of articles begins to decline. However, this decline does not necessarily mean that *The New York Times* published fewer articles overall. It may also reflect changes in the data source, archive structure, front-page selection, digital publishing, or the collection method.

Therefore, this chart is useful for identifying patterns within the dataset, but it should be interpreted as a measure of article volume in the collected data rather than a complete measure of all NYT journalism.

---

# Country Mention Analysis

## Counting Top Countries by Decade

### Process Explanation

The second part of the analysis examined which countries were mentioned most often in front-page headlines and abstracts. The dataset was divided into five time periods:

- 1980 to 1989
- 1990 to 1999
- 2000 to 2009
- 2010 to 2019
- 2020 to 2024

For each decade, the code loads the CSV file using pandas. It then uses a manually created list called `COUNTRIES`, which includes country names from around the world. This list serves as a reference for identifying country mentions in the article text.

To count the mentions, the code uses `Counter()` from the `collections` library. The code loops through each row of the dataset and checks two columns: `headline` and `abstract`. For each article, it checks whether any country name from the `COUNTRIES` list appears in either the headline or abstract.

The search is case-insensitive, so words such as “China,” “china,” and “CHINA” are treated the same. Whenever a country name is found, the code adds one count to that country in `country_mentions`.

After checking all articles, the code uses `most_common(5)` to return the five countries with the highest number of mentions. In some cases, `most_common(6)` was used because the United States appeared in the results, and the goal was to focus more on how U.S. media represented other countries.

### Method Limitation

One limitation of this method is that it only searches for exact country names in the headline and abstract. It does not count related references such as city names or political leaders. For example, “Beijing” may refer to China, and “Xi Jinping” may also indicate China-related coverage, but those references are not counted unless the word “China” appears directly.

Another limitation is that the original text-matching method may overcount some countries. For example, “Oman” could accidentally be counted inside words like “woman” because the code checks whether the country name appears anywhere inside the text. Because of this, the chart is useful for showing general patterns, but the results should be checked carefully before making strong conclusions.

### Result Interpretation

<!-- Insert your country mention chart here -->
<!-- Example: ![Country Mentions by Decade](country_mentions_by_decade.png) -->

In the 1980s, Israel, Japan, Iran, Lebanon, and Oman appeared frequently. This may connect to Cold War-era international coverage, Middle East conflicts, Iran-related news, and Japan’s strong economic presence during that decade.

In the 1990s, Japan remained highly mentioned, while Russia became more prominent. This may reflect the collapse of the Soviet Union and post-Cold War changes. Israel continued to appear, but with fewer mentions than in the 1980s.

The 2000s show the clearest peak, especially for Iraq, with around 6.1K mentions. This makes sense because the Iraq War began in 2003 and became one of the most important international news stories of the decade. China, Japan, Israel, and Oman also appeared often, suggesting that the Middle East and Asia were major areas of coverage.

In the 2010s, the number of mentions became lower overall. China remained one of the most mentioned countries, likely because of its growing global economic and political importance. Russia, Syria, Iran, and Ukraine also appeared frequently, which may relate to the Syrian Civil War, Russian foreign policy, and the conflict involving Ukraine beginning in 2014.

For 2020 to 2024, China, Russia, and Israel remained visible. This period may reflect coverage of COVID-19, U.S.-China tensions, the Russia-Ukraine war, and the Israel-Gaza conflict. However, the counts are lower partly because this period only covers five years, while the other time periods cover ten years.

---

# Topic Modeling

## Unsupervised Machine Learning Approach

### Process Explanation

The final part of the analysis used BERTopic to identify major themes in the newspaper abstracts. The process began with the `abstract` column. Before modeling, the text was cleaned by removing missing values, numbers, punctuation, and unnecessary words. This step helps reduce noise in the dataset so the model can focus on the main meaning of each abstract.

After cleaning, each abstract was converted into an embedding using `SentenceTransformer()`. An embedding is a numerical representation of text meaning. This allows articles with similar content to have similar vector representations.

Next, the model applied dimension reduction and clustering. UMAP was used to reduce the size of the vector space while keeping the main structure of the data. This makes the embeddings easier to group. HDBSCAN was then used to cluster similar abstracts together based on semantic similarity. Articles that did not clearly belong to a group were treated as outliers.

After the abstracts were embedded, reduced, and clustered, BERTopic fit the topic model. It identified topics by grouping related articles and extracting representative words for each topic.

Overall, this process turns a large collection of newspaper abstracts into organized topic groups. Instead of reading every abstract manually, BERTopic helps summarize the major themes that appear across the articles.

---

## BERTopic Result Interpretation
Initially, we decided to run separate models for each mentioned decade. Here is an example of our results from the models (2020-2024).

![model-result](https://github.com/tu-tong/NYT-Front-page-Coverage-Analysis/raw/main/Initial%20Results/topic-modeling-result-example.png)

The BERTopic results show the main topic clusters found in the newspaper abstracts. Each row represents one topic. The `Count` column shows how many abstracts belong to that topic, the `Name` column shows the model-generated topic label, and the `Representation` column shows the most important words used to describe the topic.

The largest group is Topic `-1`, with 6958 articles. In BERTopic, Topic `-1` usually represents outliers, meaning documents that the model could not clearly assign to one specific topic. In this model, Cluster '-1' does not capture the overall topic well.

Other topics show more specific themes. For example, Topic 0 focuses on the U.S. politics, with words such as “trump, president, republican, election, donald, former, party, democrat, biden, voter”. Topic 1 focuses on media and entertainment, with words such as “actor, film, series, comedy, movie, star, actress, drama, netflix, tv”. Topic 2 focuses on Russia-Ukraine War.

Other topics include U.S.-China trade, mixed sports, and art.

## Topic Interpretation

We then chose top 7 topics from each model (decade) and transform it into more english-friendly words for readability, divided into general topics, and specific subtopics.

Initial model results are shown below:

![topic-assignment-distribution](https://github.com/tu-tong/NYT-Front-page-Coverage-Analysis/raw/main/Initial%20Results/main-topic-distribution.png)
![by-decade](https://github.com/tu-tong/NYT-Front-page-Coverage-Analysis/raw/main/Initial%20Results/topic-distribution-by-decade.png)
![geopolitics-subtopics](https://github.com/tu-tong/NYT-Front-page-Coverage-Analysis/raw/main/Initial%20Results/geopolitics-subtopic.png)
![arts-subtopics](https://github.com/tu-tong/NYT-Front-page-Coverage-Analysis/raw/main/Initial%20Results/arts-subtopic.png)
---

## Assigning Topics Back to the Dataset

The topic results were then assigned back to the dataset. We will also add this step to our final analysis presentation, as it makes sure our topic assignment is correct and meaningful.

```python
df_topic['topic'] = topics_model
df_topic['topic_prob'] = [p.max() for p in probs]
```

The `topic` column shows the topic number assigned to each article, while the `topic_prob` column shows the model’s confidence in that assignment. This makes it possible to analyze articles by topic, compare topic frequency, filter for high-confidence results, and connect the topic modeling output back to the original newspaper abstracts.

---

# Implications for Stakeholders

## Media Scholars and Journalists

This project provides insight into long-term patterns of media attention and editorial priorities. It helps show how news institutions shape public awareness of global events by deciding which countries and issues receive front-page coverage.

## Historians and Political Scientists

The findings can help contextualize U.S. perspectives on international relations across different historical periods. The results also show how wars, diplomacy, and global power shifts influence media attention.

## Policy Analysts and International Relations Researchers

This project helps identify which countries have historically dominated U.S. media attention and how that visibility may align with U.S. foreign policy priorities.

---

# Ethical, Legal, and Social Implications

This project examines how U.S. media attention toward different countries has changed over time. It also shows how major global events, such as wars, political conflicts, and economic crises, influence which countries dominate U.S. news coverage.

By analyzing front-page headlines and abstracts, this study treats media coverage as a reflection of collective awareness and national priorities in the United States. However, media coverage should not be understood as an objective measure of global importance. Instead, it should be seen as a lens that shapes public perception of the world.

---

# Conclusion

This project shows that *The New York Times* front-page coverage changes across time in both volume and focus. The yearly article count reveals shifts in front-page article frequency, especially the rise in the early 2000s and the decline after 2008. The country mention analysis shows that international attention is not evenly distributed, and certain countries become more visible during periods of war, political conflict, economic growth, or global crisis.

The BERTopic model adds another layer to the analysis by identifying major themes within the abstracts. These topics show that country coverage is not only about how often a country is mentioned, but also about the kinds of topics are being prioritized on the frontpage.

Overall, the findings suggest that U.S. media coverage plays an important role in shaping how readers understand international politics and global events.
