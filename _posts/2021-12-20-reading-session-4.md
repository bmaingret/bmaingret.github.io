---
category: [reading]
tags: [datascience, reading, machine learning, MLOps, time series, dataforgood, health]
title: "Reading session #4"
---

## Articles

- [How AI can improve products for people with impaired speech](#how-ai-can-improve-products-for-people-with-impaired-speech)
- [Unlocking human rights information with machine learning](#unlocking-human-rights-information-with-machine-learning)
- [A $9B AI Failure, Examined](#a-9b-ai-failure-examined)
- [Machine Learning Model Development and Model Operations: Principles and Practices](#machine-learning-model-development-and-model-operations-principles-and-practices)
- [Avoid These Mistakes with Time Series Forecasting](#avoid-these-mistakes-with-time-series-forecasting)

<!-- -->


## How AI can improve products for people with impaired speech

Source:
* [blog.google.com](https://blog.google/outreach-initiatives/accessibility/impaired-speech-recognition/)
* [als.net](https://www.als.net/news/als-tdi-and-google-collaborate-to-bring-ai-to-als/)

> Some have recorded hundreds or thousands of specific phrases in order to train and optimize Google’s AI-based algorithms 

Voice recognition models are built from thousands of speech recording but none of them are from people suffering speech impairment. ALS TDI joined forces with Google and recruited people with ALS to record thousands of voice examples. By training their voice recognition models with those recordings they manage to improve their model to recognize impaired speech. They do not provide any specific number on the accuracy improvment.

Once again, data is the key in those deep learning models, but it also nice to see it working!

## Unlocking human rights information with machine learning

Source: [blog.google.com](https://www.blog.google/outreach-initiatives/google-org/unlocking-human-rights-information-with-machine-learning/)

> they’ve built new tools that can automatically tag human rights documents so they are searchable — making the curation process 13 times faster

Surveying the evolution of human rights accross the globe is a challenging and time consuming task! HURIDOCS built several models to help processing human rights information corpus, extracting and classifying relevant data.

In addition to winning the Peace and Justice Strong Institutions Award at the 2021 edition of CogX, they also offer a key tool as open source: [Uwazi](https://huridocs.org/technology/uwazi/) that allows human rights defenders to store, organize and search through collections of human rights information.


## A $9B AI Failure, Examined

Source: [linkedin.com](https://www.linkedin.com/pulse/9b-ai-fail-gianluca-mauro/)

This has already made the news multiple times: Zillow, an online real estate marketplace, lost hunderds of millions in addition to their stock going down. And this because of a poort usage of a ML model.

Basically one of their business was to estimate house prices and buy them to their owner in the perspective of making a benefit on its future sell. For that they use a ML algorithm which presumably was from the Kaggle competition they conducted. Sadly the real estate market is not so simple. The article points out a few key issues:

* Real estate market is not stable and is extremely subject to external effects (c.f. Covid-19)
* Zillow wanted to renovate home before selling them. However what happens if instead of selling the home in 2 months, you have to wait 6, 12 or even more because of delays...
* You have to think upfront who is willing to sell their home fast directly to Zillow. No visit of the property before buying?!
* This one is very interesting: if your model is right 95% of the time, meaning 2.5% of the time it will be under the right price, it is safe to assume most people won't sell you these houses. This would end up with only buying houses either at the right price or to high.
* Sometimes the smallest change makes all the difference. AI models can't encode them all. For example a difference of a few street number can drastically change the price.
* Last one is golden: a senior DS job offer focusing on Facebook Prophet library skill. This might show that the ML managment behind Zillow's algorithm may not have had the focus on the right things.


## Machine Learning Model Development and Model Operations: Principles and Practices

Source: [kdnuggets.com](https://www.kdnuggets.com/2021/10/machine-learning-model-development-operations-principles-practice.html)

This article summarize all the steps of deploying a ML model in production, including key part as model performance monitoring or model version management. I think this article transpires one key issue as of today: the number of steps and tools can be overwhelming. You can go the simple way, with custom Python code and some DevOps principles, but that either will not scale very well or will rqeuire a lot of effort and ressources to grow. And once you have a lot of resources available, it will make it more difficult to work on the same project. Cloud platform solutions can then be of help, making complex tools easily available but at the cost of being more tightly coupled with their platform.

## Avoid These Mistakes with Time Series Forecasting

Source: [kdnuggets.com](https://www.kdnuggets.com/2021/12/avoid-mistakes-time-series-forecasting.html)

Sometimes we want to quickly see if our data is anything but random, so we generate random samples and compare simple metrics to see if there is any significant difference. However if we don't pick the right distribution or random generator, it is easy to get fouled. 

In this example, they make the point that market stock price time series shouldn't be compared to random samples drawn from a normal distribution, but to random walk generated numbers.

In the same way, it is easy to stop as soon as we identify any relevant differences. Such when they compare the differenced time series which seem to be different, but once you compare their autocorrelation, this is not the case anymore.

It is easy to fall in "you only find what you are looking for" pit, and one must be careful and prefer a consistant approach to data anlysis.
