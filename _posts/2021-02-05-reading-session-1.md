---
category: [reading]
tags: [devops, apache, python, vscode]
title: "Reading session #1"
---

## Articles

- [Apache Superset](#apache-superset)
- [Create a DevOps culture with open source principles](#create-a-devops-culture-with-open-source-principles)
- [Google recommits to the Python ecosystem](#google-recommits-to-the-python-ecosystem)
- [Now announcing: Makefile support in Visual Studio Code!](#now-announcing-makefile-support-in-visual-studio-code)
- [Abracadabra! Bringing the magics to xeus-python](#abracadabra-bringing-the-magics-to-xeus-python)

<!-- -->

## Apache Superset

Source: [superset.apache.org](https://superset.apache.org/)

> Apache Superset (Incubating) is a modern, enterprise-ready business intelligence web application.

![Superset gallery](/assets/2021-00-00-reading-sessions/superset2021-02-05%20180319.png)

Some comments:

* "Enterprise-ready" might be a stretch from others experiences
* It seems to me to be more appropriate to be used with a single consolidated database (think datalake) than multiple databases
* Some advanced charts but with simple/cleaned data. It really is a visualization tool, all preprocessing must be done before.

## Create a DevOps culture with open source principles

Source: [https://opensource.com/article/20/12/remote-devops]()

I find this article to provide reasonable guidelines that are applicable to IT departments (and probably others) being remote or not.

Open source principles:

### Community

> Being part of team goals can help people escape the stress of the home front.

Nothing worse than to be home alone facing an issue with no support.

### Collaboration

>  Collaboration—during a pandemic or not—is about culture, not the latest tool or platform.

Oftentimes people look for new tools to help them to better collaborate whereas mindset

### Transparency

>   Remote DevOps teams benefit from centralizing access to project information and materials.

Although I find that every information shouldn't be directly address to everyone, there is nothing worse than withholding information. I think everyone is able and should be allowed to comment and give one's opinion.

### Release early and often

>  When a remote DevOps team releases early and often, they prove the remote work model's validity and give stakeholders something real to see

Although in some companies and industries it is not welcome to come with unfinished projects, I find it rewarding and motivating for everyone. Good balance is necessary since each release does bring additional work.

### Pivot and refresh

>   Just as you stop to correct software delivery issues, you need to start doing the same with communications and collaboration.

When you find something did not happened as expected because of poor communication, don't mourn on it, take it as an opportunity to make some changes.

## Google recommits to the Python ecosystem

Source: [https://sdtimes.com/softwaredev/google-recommits-to-the-python-ecosystem]()

Google cloud environment was my [first experience with Python](https://github.com/bmaingret/kaist-wst660-gae-app) thanks to their Google App Engine. 

It is always important for large companies to put significant amount of support to these technologies considering how they build on it, while being careful not to fall in situations similar to Oracle/SUN and the Oracle/Java ecosystem.


## Now announcing: Makefile support in Visual Studio Code!

Source: [https://devblogs.microsoft.com/cppblog/now-announcing-makefile-support-in-visual-studio-code]()

Although make and makefile are more than 50 year old, and are not the new shiny tools, they can be of great help in data-science projects.

There is usually a lot of similar steps required to set-up environments, run different steps (data ETL, training models, evaluation, etc.), managing cloud resources, etc. which can greatly be speed up and made less error-prone thanks to makefiles.

Another article presenting some of it: [https://medium.com/@davidstevens_16424/make-my-day-ta-science-easier-e16bc50e719c]().


## Abracadabra! Bringing the magics to xeus-python

Source: [https://blog.jupyter.org/abracadabra-bringing-the-magics-to-xeus-python-9d17bcfacb4]()

It is always interesting to read documentation on the behind the scenes of some of the tools we used. If find it greatly enhance they way I work with them by getting a glimpse of how and why the tool was made as it is and where it is going.

This is article presents the work on the [next Jupyter kernel](https://blog.jupyter.org/a-new-python-kernel-for-jupyter-fcdf211e30a8), partly based on Xeus a C++ implementation of the Jupyter kernel protocol. And reading this, all of a sudden we can discover some of the components of what  we usually call a Jupyter notebook...
