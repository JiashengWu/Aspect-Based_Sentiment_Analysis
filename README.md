# Aspect-Based Sentiment Analysis for Patient's Narrative

*by* **Vanilla Deep** ( Jianheng Hou, Zheng Cao, Jiasheng Wu, Yuang Liang )

## Introduction

### Background

As known to all, data science and analysis has brought innovation to the financial investment industry in recent years. The idea of this project came from venture capital field: partners in firm want to leverage data science to extract insights hiding in the data on healthcare field. This is an innovative pipeline to dig out and monitor patient’s suffering, or say, issues in healthcare so to support investment strategy of venture capital (VC) firms.  This is a typical end-to-end Text Mining Project. Aspect-based sentiment analysis, as the main part of the project, is used to analyze the sentiment of patient’s narrative with different aspects we defined.

### Problem Statement

Different from traditional sentiment analysis, we analyze sentiment by different issue categories which are predicted by another model. 

<p align="center">
  <img src="fig/Problem_Formulation.png" style="max-height: 300px" />
  <em>Problem Formulation</em>
</p>

Given each post of patient’s narrative, multi-label classification predicts what types of issues this post mentions. We defined 5 types of issues in healthcare I are interested. Then sentiment classification model predicts the sentiment of the post. We defined 2 different levels of sentiment, i.e. 1. Non-negative; 2. Negative. By this way, it is easier to know the sentiment distribution of tons of posts from different aspects that we defined.

### Challenges

The first challenge comes from the annotation. We collect data without labels from the biggest patient online forum ([patient.info](https://patient.info/)) in U.K and the U.S. We trained data with ground truth labeled by annotators in the market, so the performance of our model was limited to the quality of this data we collected. The second challenge is high imbalance of the dataset we created, in which the data from the majority class occupies 87% of all. The third challenge is that multi-label classification made our models hard to train, because insufficient minority data might not be learnt by models and the choosing of metrics for loss function and evaluation is a trick during training stage. To generate readable and intuitive visualization based on the output of models and to extract actionable insights from it is the last challenging point.

## Pipeline

<p align="center">
  <img src="fig/Pipeline.png" style="max-height: 300px" />
  <em>Pipeline</em>
</p>

The diagram above shows the whole pipeline of our end-to-end project, which includes data preparation, model training, visualization, and analysis. After crawling raw data (patient’s narrative posts) from the forum, we stored data in Amazon S3 and then followed by text processing using Spark. As our models are supervised based, we need to collect ground-truth label of the data, so we collect them through an online service, Amazon Mechanical Turk.  Entity recognition and relation recognition was done for feature engineering then. After generating and finalizing training dataset, models were built using deep learning frameworks: Keras and Pytorch. The model with the best performance was used to generate prediction results for the rest of all data other than training dataset, from which we got a list of meaningful and insightful insights via visualization and statistical analysis.

## Data Preparation

We used Scrapy to collect around 1.3 million patients’ narratives from the biggest patient online forum ([patient.info](https://patient.info/)) in U.K and the U.S. as our dataset. In order to annotate our data as quickly and accurately as possible, we designed a survey and sent them out to collect labels on Amazon Mechanical Turk. We embedded a sample of size 5,000 that annotated by our own in the survey with 50,000 posts we sent out to check the performance of workers. 

<p align="center">
  <img src="fig/Amazon_Mechanical_Turk.png" style="max-height: 300px" />
  <em>Annotation using Survey on Amazon Mechanical Turk</em>
</p>

Raw texts always contain wrong spelling, redundant punctuations, meaningless information. We filtered punctuations, extended contractions and abbreviations via dictionaries, parsed part of speech, made words lowercase, corrected spelling, and so on. All of these methods are beneficial to the performance of our models.

Features are important to both non-deep learning models and deep learning models. We use CLAMP toolkit to extract medical entities and relations in free text as features in non-deep learning models.

<p align="center">
  <img src="fig/CLAMP.png" style="max-height: 400px" />
  <em>Entities and Relations Extraction using CLAMP</em>
</p>

## Model Training

We tried three different kinds of word embeddings: `FastText.300d` embedding (trained by us on the dataset) and other two popular pre-trained word embedding, `glove.840B.300d` and `glove.twitter.27B`. It turned out our FastText embedding led to a better performance of models and we ascribed this to more domain-oriented clinical terminologies in the corpus. Below is the T-SNE visualization of the post embedding across 10 common disease topics. We do see some clusters, while the overall clustering effect is not as great as we expected due to noisy post or general discussions.

<iframe src="http://htmlpreview.github.io/?https://github.com/JiashengWu/Aspect-Based_Sentiment_Analysis/blob/master/fig/Top_10_Disease_Categories.html" width="1016px" height="766px">
  <p align="center">
    <img src="fig/Top_10_Disease_Categories.png" style="max-height: 400px" />
    <a href="http://htmlpreview.github.io/?https://github.com/JiashengWu/Aspect-Based_Sentiment_Analysis/blob/master/fig/Top_10_Disease_Categories.html">[Interactive Chart]&nbsp;</a>
  </p>
</iframe>
<p align="center">
  <em>T-SNE Embedding of Top 10 Disease Categories</em>
</p>

We built a ensembled logistic regression and a neural network with three linear layers as the baseline of the project, and spent more time on 3 different types of deep learning models as below:

<table>
  <tr>
    <td style="background: none">
      <img src="fig/Model_1.jpeg" />
    </td>
    <td>
      <img src="fig/Model_2.jpeg" />
    </td>
    <td>
      <img src="fig/Model_3.png" />
    </td>
  </tr>
  <tr>
    <td align="center">
      <em>Pooled RNN</em>
    </td>
    <td align="center">
      <em>Pooled RNN with TextCNN</em>
    </td>
    <td align="center">
      <em>BERT</em>
    </td>
  </tr>
</table>

After a bunch of experiments and model tuning, Pooled RNN (avg f1: 0.566) and BERT (avg f1: 0.557) led to better performance on the test set. Since they caught different things as shown in the accuracy of all data and the accuracy of all data exclude data without any target labels, I ensembled them together to output the best performance in the end (avg f1: 0.571).

<p align="center">
  <img src="fig/Evaluation.png" style="max-height: 300px" />
  <em>Evaluation of Models</em>
</p>

## Visualization

<p align="center">
  <img src="fig/Aspect-Based_Sentiment_Analysis_for_Top_10_Disease_Categories.png" />
  <em>Aspect-Based Sentiment Analysis for Top 10 Disease Categories</em>
</p>

<p align="center">
  <img src="fig/Sentiment_Statistics_by_Aspects_across_10_Common_Topics_in_Health_Care.png" />
  <em>Sentiment Statistics by Aspects across 10 Common Topics in Health Care</em>
</p>

## Summary

## References
