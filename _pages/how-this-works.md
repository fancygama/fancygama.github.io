---
title: How This Works
layout: single
permalink: /how-this-works/
author_profile: true
toc: true
---

## Overview

Sports Sentiment attempts to measure the degree of positivity or negativity expressed in sports conversations, more specifically in comments from leagues&#39; and individual teams&#39; subreddits (forums) on Reddit. The model that takes raw text and outputs a positivity/negativity score was trained and validated to \&gt;80% accuracy on 1.6 million tweets. Sentiment Analysis has been shown to be highly transferable, so training on tweets and predicting on Reddit comments should not see a huge drop in performance, although there is no way to measure this currently. Data has only been collected since July 2020, but early results do show [a strong association between fanbases&#39; sentiment and win-loss results](#model-performance), suggesting the validity of this model and its usefulness as an approximate litmus test for fans&#39; mood.

Solely using Reddit comments may limit the scope of analysis to less casual, more &quot;nerdy&quot; fans, but it has the advantages of:

- Subreddits classified by team (as opposed to Twitter)
- A free and fast API for collecting data, and a demographic slowly shifting toward that of the larger sports fan population due to the site&#39;s growth
- Similar rituals across subreddits (pregame/in-game/postgame threads) that make comparing two teams in the same league a worthwhile exercise

To sum up, I hope Sports Sentiment can be a fun diversion, showcasing the strength of Machine Learning (ML) in understanding internet language, and providing a way to visualize teams&#39; highs and lows over the course of an unusual 2020 season. I encourage you to add to your experience by cross-reference movements in team graphs with news from specific days (e.g. observe the increase in negativity for just about every MLB team when it looked like the season would be cancelled after the Marlins outbreak on July 27). Look out for updates on this page in the future.

## How Does the Model Work?

Sports Sentiment uses a neural network to generate scores corresponding roughly to how positive or negative the mood of a comment is. This is an example of a Natural Language Processing technique known as Sentiment Analysis.

Unlike models for precise tasks such as fraud detection, strong Sentiment Analysis models may have an accuracy of just 70-80%, simply because sentiment is **subjective** : even [humans only agree with one another on 80%](https://mashable.com/2010/04/19/sentiment-analysis/) of examples. Various model types can perform Sentiment Analysis with reliable accuracy, ranging from linear regression to ML techniques like Naive Bayes. I used Deep Learning, in particular a Recurrent Neural Network (RNN), because;

- With sufficient data, Neural Networks can model complex interactions between different inputs (words in this case) without much more than raw examples. Other ML algorithms depend more on manual [Feature Engineering](https://machinelearningmastery.com/discover-feature-engineering-how-to-engineer-features-and-how-to-get-good-at-it/), which is time consuming and depends on extra [domain knowledge](https://medium.com/@anand0427/why-domain-knowledge-is-important-in-data-science-anand0427-3002c659c0a5). With vast enough quantities of relevant data, Neural Nets can tackle end-to-end problems that normally require multiple stages of specially designed algorithms, such as [speech recognition](https://heartbeat.fritz.ai/the-3-deep-learning-frameworks-for-end-to-end-speech-recognition-that-power-your-devices-37b891ddc380).
  - Note: the model doesn&#39;t really &quot;understand&quot; the meanings of words in the way humans do. &quot;Neural&quot; Nets conjure images of the human brain, but the human brain is infinitely more complex. There&#39;s also no reason to think the human brain is the optimal blueprint for all ML; take the failure of classical contraptions that focused on resembling birds, who happen to be able to fly, rather than just focusing on flying.
- RNNs are designed to &quot;memorize&quot; words earlier in a comment and apply that stored contextual knowledge to words near the end of it.
  - Take the sentence, &quot;The Nationals rallied, but Seattle&#39;s dominant bullpen regained its footing to hold on to the win.&quot; Other types of models may infger &quot;Seattle&quot; is referring to the city as a whole, but an RNN is well built to decipher that it&#39;s referring to the Mariners because it stores context supplied by &quot;Nationals&quot; for later use.
  - RNNs are also known as Sequence Models and can be applied to other sequenced data besides strings of text such as audio.
    - This model is a GRU RNN, which stands for Gated Recurrent Unit, but essentially just means it&#39;s better equipped to &quot;memorize&quot; information for longer distances within a sentence than basic RNN&#39;s. LSTM&#39;s are another population alternative with a similar goal, but they are generally slower to train.
- RNNs have additional potential in the form of Bidirectional variants, which can use words _later_ in a comment to contextualize words near the beginning by reading backward, on top of the normal forward behavior.
  - Take the sentence &quot;Teddy lived in Oyster Bay, while Franklin lived in Hyde Park.&quot; The normal, forward model sees that &quot;Franklin&quot; probably means FDR because it remembers also seeing &quot;Teddy&quot;, but it wouldn&#39;t know if &quot;Teddy&quot; was the president or the stuffed animal because it can&#39;t iterate backward once it sees &quot;Franklin&quot;. A Bidirectional model solves this issue.

### Transfer Learning

Other sentiment models are commonly trained on Amazon or IMDB ratings to allow an explicit text to score mapping, but this one is trained on Stanford NLP&#39;s [Sentiment140](http://help.sentiment140.com/for-students) dataset, which contains 1.6 million tweets made in 2009 labeled for two-way polarity (either &quot;positive&quot; or &quot;negative.&quot;) The paper &quot;[Sentiment Strength Detection in Short Informal Text](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.278.3863&amp;rep=rep1&amp;type=pdf)&quot; suggests that review datasets may be better suited for commercial applications, while internet messages have more in common with this exercise&#39;s use case. 2009 tweets and 2020 Reddit comments certainly have different distributions in terms of the range and connotation of words they use, especially given how most of the tweets have nothing to do with sports. This is where Transfer Learning comes in.

Because Deep Learning models are made of multiple layers, it&#39;s sometimes practical to _transfer_ part of an existing model as a foundation for a new model. In this instance, I gave my model a headstart on learning semantic meanings of words by using [GloVe embeddings](https://nlp.stanford.edu/projects/glove/) from Stanford, which are trained on billions of examples. A model with stronger underlying semantic knowledge of will also be more robust when applied across text datasets with differing distributions.

### Is This Model Predictive?

This model is [explanatory rather than predictive](https://www.theanalysisfactor.com/differences-in-model-building-explanatory-and-predictive-models/#:~:text=As%20a%20result%2C%20predictive%20models,primary%20goal%20is%20predictive%20accuracy.&amp;text=Variables%20that%20are%20used%20in,statistical%20significance%20or%20scientific%20meaning.), although a model predicting how fans&#39; sentiment will change in the future could conceivably be trained on the outputs of thismodel. I plan to make this sentiment data available on GitHub for others to analyze in the near future.

### What Do Sentiment Scores Mean?

In probability models, the last layer of a model typically contains [a formula](https://deepai.org/machine-learning-glossary-and-terms/sigmoid-function) that constricts values to a range from 0 to 1. In this case, I bypassed this step, allowing outputs for individual comments to range from roughly -4 to +4. When averaged over all comments of a respective subreddit, outputs range from roughly +0.250 to +0.500. I find this range is more intuitive than the corresponding probabilities of 57% and 62% positive; recall that no comment is completely positive or negative, but that the reality is a subjective spectrum. This is similar to the &quot;sentiment strengths&quot; technique from the aforementioned [Short Informal Text](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.278.3863&amp;rep=rep1&amp;type=pdf) paper, although the methodology differs.

### How Are the Graph &quot;Averages&quot; Calculated?

There&#39;s no hard-and-fast cutoff to what comments are recent, and thus relevant to fans&#39; current mood. There&#39;s also the matter of maintaining enough sample size to prevent an individual comment&#39;s score from skewing the average over time spans where there may be few comments.

To tackle this, I use a half-life moving average. With a half life of 6 hours (as used here), a comment from 7 hours ago receives half the weight of a comment made 1 hour ago. The same goes for 15 hours ago vs. 9 hours ago, and so on. This solves both the problem of recency and sample size, since 100 comments made several hours ago will still _cumulatively_ outweigh 10 comments made in the last few minutes, but those comments from the last few minutes are still _individually_ worth more than each comment from several hours ago.

## Model Performance

There is no current way to test model accuracy in predicting sentiment on the Reddit dataset. However, we can observe a clear relationship between the way subreddit sentiment changes before and after wins vs. losses, in a technique known as [Diff-in-Diff](https://en.wikipedia.org/wiki/Difference_in_differences). Here, pre- and postgame sentiment is calculated from the average sentiment of comments in a subreddits 6 hours before and after a game, respectively. In-game comments are not included in this comparison. Win delta refers to the change in sentiment from pre- to post-win, and loss delta refers to the same quantity when considering losses.

This analysis was conducted separately on MLB and NBA data. The respective graphs below illustrate how most teams react positively to wins and negatively to losses, and how virtually all teams react more positively to wins than to losses.

{% include figure image_path="/assets/img/mlb_deltas.png" alt="Graph showing how the majority of MLB fanbases show increased sentiment after wins and decreased sentiment after losses." %}

{% include figure image_path="/assets/img/nba_deltas.png" alt="Graph showing how the majority of NBA fanbases show increased sentiment after wins and decreased sentiment after losses." %}

In terms of statistical significance, we can reject the null hypothesis that win delta is not greater than loss delta for the MLB with p &lt; 0.0001 (using the Comparison of Means test). With a smaller slate of games, we still observe that p = 0.0001 for the NBA. These strong effects are an encouraging indicator of the model&#39;s ability to chart teams&#39; seasons. With a larger sample size, future testing can be done to observe possible effects of clutch wins, blowouts, division leads, and news outside of the standings such as injuries and suspensions.

### Can&#39;t the Model Just Look for &quot;Win&quot; and &quot;Loss&quot;?

While we should expect the model to glean positive sentiment from words &quot;Win&quot; or &quot;Won&quot;, these words alone can&#39;t explain most of the gap in sentiment change in wins vs. losses. Within the MLB dataset, &quot;Win,&quot; &quot;Won,&quot; and similar variations are used in 10.4% of comments post-win. They&#39;re also used in 10.0% of comments _post-loss_. Variations around &quot;Lose&quot; and &quot;Loss&quot; are used in 4.0% of comments post-loss, and 2.6% of comments post-win. Even if the model hyper-focused on these words, it wouldn&#39;t only account for a roughly 1.8% change in sentiment probability in the positive or negative direction, which corresponds to a sentiment score change of .070. The average observed win-loss delta in the MLB is 0.285, or four times that.

## Known Issues

- Not all subreddit comments in the observed timespan are in the dataset. This is due to the nature of the comment parsing script, as it will mark threads as &quot;read&quot; after a single pass, despite the potential for additional comments on that thread after the parsing. This is done to reduce comment parsing time by upwards of 90%, and data gaps can be mitigated by running the parsing script in the morning, when potentially busy threads are less likely to have just been created. Still, this issue is the cause for some apparent dips in comment activity during games.
- Playoff games do not currently appear in team graphs due to limitations in the Sports Reference API.
- Double header game times may appear blurred together in graphs, due to an issue with Baseball Reference marking the start times as being 30 minutes apart.
- Suspended games are marked as if they were completed on graphs.

## Tools Used

- [PRAW](https://praw.readthedocs.io/en/latest/)
- [Sportsreference](https://sportsreference.readthedocs.io/en/stable/index.html)
- [Tensorflow 2.0](https://www.tensorflow.org/overview)
- [Plotly](https://plotly.com/python/)
- [Pandas](https://pandas.pydata.org/docs/)
- [NumPy](https://numpy.org/)