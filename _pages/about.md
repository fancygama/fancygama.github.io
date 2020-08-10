---
title: About
layout: single
permalink: /about/
author_profile: true
toc: true
---

## Overview

As the title suggests, Sports Sentiment attempts to measure the degree of positivity or negativity expressed in sports conversations, more specifically in comments from leagues&#39; and individual teams&#39; subreddits (forums) on Reddit. The model that takes raw text and outputs a positivity/negativity score was trained and validated to &gt;80% two-class (positive vs. negative) accuracy on 1.6 million tweets. Although the model performance is likely lower on Reddit comments, sentiment analysis has been shown to be highly transferable, and the somewhat arbitrary, non-scientific nature of this site makes extra tuning unnecessary at this time. Early returns since July 2020 do show a high correlation (numbers forthcoming) between fanbases&#39; sentiment and win-loss results, demonstrating again the transferability of this model and its usefulness as an approximate litmus test for fans&#39; mood.

Still, using just Reddit may limit the scope of analysis to less casual, more &quot;nerdy&quot; fans, but it has the advantages of forums classified by team (as opposed to Twitter), a free and fast API for collecting data, and a demographic slowly shifting toward that of the larger sports fan population due to the site&#39;s growth. Team forums have idiosyncrasies but tend to have similar rituals in terms of pregame/in-game/postgame threads that make comparing two teams in the same league a worthwhile exercise.

To sum up, I hope Sports Sentiment can be a fun diversion, showcasing the strength of Machine Learning in understanding internet language, and providing a way to visualize teams&#39; highs and lows over the course of an unusual 2020 season. I encourage you to add to your experience by cross-reference movements in team graphs with news from specific days (e.g. observe the increase in negativity for just about every MLB team when it looked like the season would be cancelled after the Marlins outbreak). Look out for updates on this page and contact me on Twitter for any questions or comments.

## How Does the Model Work?

Sports Sentiment uses a neural network to parse raw text and predict whether the person who wrote it was expressing positivity or negativity, in a Natural Language Processing technique known as Sentiment Analysis. Other sentiment models are commonly trained on Amazon or IMDB ratings to allow an explicit text to score mapping, but this one is trained on Stanford NLP&#39;s [Sentiment140](http://help.sentiment140.com/for-students) dataset, which contains 1.6 million tweets from April 2009 labelled either &quot;positive&quot; or &quot;negative&#39;. I filter out punctuation and emojis from raw text, which certainly loses some information but allows me to perform &quot;Transfer Learning&quot; -- that is, I can apply part of a huge model trained by researchers for a separate task on a vastly larger, _big bro_ of a dataset to save training time on my computer&#39;s end and to give my model some extra &quot;wisdom&quot; about the way words in the English language relate. (For those curious, this part of the _big bro_ model is known as a set of [Word Embeddings](https://www.youtube.com/watch?v=uTL9tm7S1Io).)

While many types of models can perform Sentiment Analysis with reliable accuracy, ranging from linear regression to Machine Learning techniques like decision trees and text proximity (similarity) analysis, I employed the subset of Machine Learning known as Deep Learning for this task because:

- Deep Learning sounds cooler -- most models are referred to as &quot;Neural Networks.&quot;
- Neural Nets can model complex interactions between different words in a sentence without humans needing to explicitly provide labelled examples of those interactions.
- Recurrent Neural Networks (RNNs), which this model is an example of, are excellent at &quot;memorizing&quot; words earlier in a comment and applying that stored knowledge to words near the end of it.
  - For example, take the sentence, &quot;The Nationals rallied, but Seattle&#39;s dominant bullpen regained its footing to hold on to the win.&quot; Other types of models may infger &quot;Seattle&quot; is referring to the city as a whole, but an RNN is well built to decipher that it&#39;s referring to the Mariners because it stores context supplied by &quot;Nationals&quot; for later use
  - RNNs are also known as Sequence Models and can be applied to other sequenced data besides strings of text such as audio.
    - <small>This model is a GRU RNN, which stands for Gated Recurrent Unit, but essentially just means it&#39;s better equipped to &quot;memorize&quot; information for longer distances within a sentence than basic RNN&#39;s. LSTM&#39;s are another population alternative with a similar goal, but they are generally slower to train.</small>
- Within the realm of RNNs, this model is Bidirectional -- that is, it can use context from later in a comment to determine the meaning. Regular RNNs

(I should add that the model doesn&#39;t really &quot;understand&quot; the meanings of words in the way humans do, and that this is not an example of &quot;Artificial Intelligence,&quot; but for the purposes of this explanation my language should give the general gist of the underlying math.)

### Math Alert

So, with the idea in hand that a positive score for a comment indicates it is expressing positivity and vice versa, what do the full range of the numbers mean? Is a comment with a score of +1.0 twice as positive as a comment with a score of +0.5? The answer is: not quite. To make sure that those still reading click off this page, let&#39;s go through a basic example involving linear regression, the humble ancestor of Machine Learning:

You are predicting the price of houses in an area on two factors: square-foot size of the house and distance to the nearest body of water. The model you build can be represented in terms of:

1. X \* (house size) + Y \* (water distance) = price

So, your goal is to find some X and Y that best fit/explain this relationship. You&#39;ll remember X and Y are normally referred to as _coefficients_, although they&#39;re usually called _parameters_ in Machine Learning. What if instead of predicting house price itself, you want to predict the _probability_ that price is over a certain value, say $350,000? This time you&#39;d perform _logistic regression_, built specifically for regressions relating to probabilities. They work by taking the left side of the equation above and putting it through a special formula that converts that makes sure the final value is between 0 and 1, because that is the only range probabilities can take on. I&#39;ll use σ to represent the function, so the equation for this new problem is;

1. σ[X \* (house size) + Y \* (water distance)] = prob(price \&gt; $350,000)

The goal of logistic regression is still to find number values for the X and Y parameters, But remember, the left hand side only needs to be between 0 and 1 _after_ going through σ, so the expression &quot;X \* (house size) + Y \* (water distance)&quot; could itself be any positive or negative number. The nifty thing is that any positive number of this expression will spit out a probability greater than 50%, and vice versa for a negative number.

<small>(σ is nonlinear, which means σ(2) ≠ 2 \* σ(1). Read up more on the [Sigmoid function](https://en.wikipedia.org/wiki/Sigmoid_function), if you&#39;re so inclined.)</small>

Going back to the problem of Sentiment Analysis instead of housing price, recall that the data I trained on had labels for whether a tweet was positive or negative. Contrast this with IMDB or Amazon review datasets, where you&#39;re given a numeric scale. The review datasets correspond more to the regular house price example (1), and the twitter dataset I actually used corresponds to the probability example (2). Yet, my model still outputs some numbers greater than 1 and less than 0, so why is that?

Well, I trained my model to predict probabilities like in (2), but then once I got those X and Y parameters (69.5 million instead of 2 parameters in this case), I _removed_ the part where the left hand side is put through σ to be between 0 and 1. I did this because my goal is not so much to **predict** on individual comments, but to **explain** on large _sets_ of comments. Think about it like BMI measurements: for one person, the number could mean very little, given factors like muscle composition (it could even be converted to a probability that person is over-/underweight given that we know nothing else about that person), but across large populations the average BMI can be telling. Now, I&#39;m not predicting sports fans&#39; BMI and I&#39;ll leave that task to Charles Barkley, but my overall goal is to look at fanbase sentiments over certain periods of time by averaging over large amounts of comments.

Lastly, it&#39;s possible to build a model detecting sentiment toward a specific thing in a comment rather detecting the general sentiment of the comment as a whole, but this &quot;aboutness&quot; problem is considerably more complex and requires a different kind of training data than Sentiment140. I&#39;m making the reasonable but not air-tight assumption that the majority of comments in a team&#39;s subreddit are from fans of that specific team, which seems to hold true given glimpses into theses subreddits and the intuitiveness of graphs&#39; up-and-down movements as compared to team performance.

### How Are the Graph &quot;Averages&quot; Calculated?

Given what I just described about comment averages, you can maybe envision how the tables on league landing pages, showing teams&#39; sentiment over, say, the last 24 hours, are calculated. I simply take all comments over that timespan and average out their sentiment scores, as provided by my model.

But what about the line graphs for each team that show moving sentiment averages over time? There&#39;s obviously no hard-and-fast cutoff to what comments are considered recent and relevant to fans&#39; current mood versus too old, and there&#39;s also the matter of maintaining enough sample size to prevent an individual comment&#39;s score from dominating the average over time spans where there may be few comments.

To tackle this, I use a half-life moving average, which may sound familiar to Chemistry students. The half life I use is 6 hours, so a comment from 7 hours ago receives half the weight of a comment made 1 hour ago, the same goes for 15 hours ago vs. 9 hours ago, and so on. This solves both the problem of recency and sample size because 100 comments made a few hours ago will still _cumulatively_ outweigh 10 comments made in the last few minutes, but those comments from the last few minutes are still _individually_ worth more than each comment from a few hours ago.

(I also use a tiny bit of line smoothing on top of this weighted average to better capture trends, because jagged graphs break backs).

## Tools Used