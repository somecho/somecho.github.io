---
title: "B.R.A.H.M.S. Explorer"
date: 2023-03-05T12:56:14+02:00
draft: false
summary: "The B.R.A.H.M.S. Explorer is a searchable catalog for contemporary music. It uses React for the frontend, Flask on the backend and Sqlite as a database, running as a Docker Web Service on Render."
tags: ["React","Flask","Sqlite","Docker","JavaScript","Python","Sql"]
emoji: "📇"
---

[The B.R.A.H.M.S.
Explorer](https://github.com/somecho/catalog-for-contemporary-music)
is a searchable catalog for contemporary music with a React frontend
and a Flask backend. It uses Sqlite as a database with data is scraped
from [IRCAM's website](https://brahms.ircam.fr/en/). The live application is hosted [on
Render](https://catalogforcontemporarymusic.onrender.com/) as a Docker
web service[^1].

{{< image src="/images/brahms/ui.png" 
alt="The mobile and desktop user interface"
caption="The mobile and desktop user interface">}}

## Goal

The original search form on IRCAM's website is cluttered, with many advanced
options, such as whether a piece was wrote in IRCAM, or who the musical
assistants are. Searching by instrumentation was also unintuitive, as arbitrary
groupings of instrumentation categories are used. The results of the search also
could not be sorted.

{{< image src="/images/brahms/ircam.png"
alt="Screenshot of ircam's instrumentations"
caption="IRCAM's instrumentation groupings" >}}

As a musician myself, I wanted to be able to just enter some loosely related
terms, like "viola tuba clarinet" and to see which pieces had them. I also
wanted to be able to sort the results, either by composer name, year or title.

## Search

The search algorithm is simple. It splits up the keyword terms by space. Then
an SQL query is made to the database making sure all terms in the keywords
appear either in the title, the subtitle, the composer's name or the year. This
means that a broader of results will be available. The results can also be
sorted in ascending or descending order.

{{< image src="/images/brahms/searchbar.png"
alt="screenshot of searchbar"
caption="the searchbar" >}}

## Keeping the database updated.

Since the entire catalog runs in a docker container on Render, there is no
persistent memory. If I wanted to keep the database updated, I would have to
update my Sqlite database locally and push it to github. 

The solution I opted for was to write a Github Action that scrapes IRCAM's
website weekly, adds new pieces and composers to the database and then pushing
it to repo. The web service on Render would then redeploy with this new update.


[^1]: With free tier hosting, the performance of the web service may
	be slow.
