---
category: [reading]
tags: [datascience, reading, machine learning, python, ETL]
title: "Reading session #3 - 2021-04-21"
---

## Articles

- [Python in Visual Studio Code – April 2021 Release](#python-in-visual-studio-code--april-2021-release)
- [Is manual ETL better than No-Code ETL: Are ETL tools dead?](#is-manual-etl-better-than-no-code-etl-are-etl-tools-dead)
- [The Explosion of Roles in Data Science](#the-explosion-of-roles-in-data-science)
- [The Sexiest Job of the 21st Century Isn’t “Sexy” Anymore](#the-sexiest-job-of-the-21st-century-isnt-sexy-anymore)
- [Data Scientist vs Machine Learning Engineer Skills. Here’s the Difference.](#data-scientist-vs-machine-learning-engineer-skills-heres-the-difference)
- [10 Tips and Tricks for Data Scientists Vol.4](#10-tips-and-tricks-for-data-scientists-vol4)


<!-- -->

## Python in Visual Studio Code – April 2021 Release

Source: [devblogs.microsoft.com](https://devblogs.microsoft.com/python/python-in-visual-studio-code-april-2021-release/)

I have been using VS Code for 2 years now, mostly for Python, including notebooks, but also for any text file. 

### Support for [Poetry](https://python-poetry.org) environments

Never used but probably will be the next I use if I ever move from conda. It ditches setup cfg an py files for the PEP pyproject.toml files. Also it allows to make the differences between what you wanted to install and every dependencies that got installed along!

### Better auto-completions for Pytorch using Pylance

Never had the opportunity to face these issues since so far I have used only TensorFlow and it was in Jupyter notebook where I have found auto-completion to be a bit clumsy.

### Data Viewer Enhancements

Data viewer is one of the reason to ditch Jupyter notebooks and use VS Code. Since I had spent some times on RStudio prior to Python for data science, this data viewer was one of the feature that was missing for me.

Enhancements listed:
* Ability to refresh the data viewer
* Support for PyTorch and TensorFlow Tensor data types
* Visual update
* Ability to slice data (huge!): easily see specific dimensions of high dimension data


## Is manual ETL better than No-Code ETL: Are ETL tools dead?

Source: [analyticsvidhya.com](https://www.analyticsvidhya.com/blog/2021/04/is-manual-etl-better-than-no-code-etl-are-etl-tools-dead/)

Try to oppose GUI tools to pure code tools for ETL purposes, which I think in most cases don't make so much sense. In most GUI tools you have a way to script some part to have custom tailored transformation for instance. And as for pure code for ETL, it can quickly become challenging and a human resource intensive process. In the end it usually comes down to the knowledge and know-how of the teams at work.


## The Explosion of Roles in Data Science

Source: [towardsdatascience.com](https://towardsdatascience.com/the-explosion-of-roles-in-data-science-5963aa83e1c)

> We have data scientists, data analysts, data engineers, machine learning engineers, analytics engineers, business intelligence engineers, data architects, data storytellers…

How to overcome the overwhelming effect of so many different roles to chose from? Especially when job offers usually mixes everything and that you have no or little prior experience in any of these roles?

* "You are not your role": don't limit yourself to the job title, roles overlap more than often, and you are not tied to a job name
* "Focus on abilities rather than on roles": As I said it is often unclear what exactly lies under each role. But abilities stay the same. Some company might think a Data Engineer is a Database Admin. So? Don't focus on the role but on the ability you'll develop: manipulating database and SQL.
* "Keep learning, keep improving": Some company focus to much on what people know at the instant they want to recruit them. Considering the pace at which Data Science is evolving I think it is fair to accept people who have the fundamental abilities required for DS/ML and who keep on learning.

## The Sexiest Job of the 21st Century Isn’t “Sexy” Anymore

Source: [medium.com/illumination](https://medium.com/illumination/the-sexiest-job-of-the-21st-century-isnt-sexy-anymore-fd5335a5d4d4)

* "#1 People Doesn’t Know What Actually Is Data Science": Be the people wanted to get into DS and people trying to recruit datascientists.
* "#2 Expectation vs. Reality — Here Lies A Wide, Wide Gap!": People getting into DS think they'll work each week on a new project with cool new tech or algorithm...
* "#3 Lack of Upskilling for Data Science Professionals": Things are moving so fast it can get hard to be an expert of anything, which companies are looking for
* "#5 People Aren’t Willing To Wait": It is a long road to become a proficient data scientist...

## Data Scientist vs Machine Learning Engineer Skills. Here’s the Difference.

Source: [towardsdatascience.com](https://towardsdatascience.com/data-scientist-vs-machine-learning-engineer-skills-heres-the-difference-93eb2f4f6f98)

This article seems just wrong to me:
* "a machine learning engineer does not necessarily need to know how random forest works, but they need to know how to save and load a file automatically".
* "If you can master these three base skills, you will be well on your way to being a great data scientist". Sills being Python, Jupyter and SQL. If that's all you require from a data scientist, look just above.

## 10 Tips and Tricks for Data Scientists Vol.4

Source: [r-bloggers.com](https://www.r-bloggers.com/2021/04/10-tips-and-tricks-for-data-scientists-vol-4/)

* You can get Google Drive data directly into Google Colab
  
```
from google.colab import drive
drive.mount('content/gdrive')
```

* Reading/Writing Pandas DF directly as GZip (use `compression='gzip'`)

Others can be checked directly but were not of much interest for me.
