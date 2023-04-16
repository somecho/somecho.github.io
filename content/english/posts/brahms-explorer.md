---
title: "B.R.A.H.M.S. Explorer"
date: 2023-03-05T12:56:14+02:00
draft: false
summary: "The B.R.A.H.M.S. Explorer is a searchable catalog for contemporary music built with a microservices architecture."
tags: ["React","Flask","MySQL","JavaScript","Python","Microservices"]
emoji: "📇"
---

[The B.R.A.H.M.S. Explorer](https://github.com/somecho/brahms-explorer) is a
searchable catalog for contemporary music with data scraped from IRCAM's
[B.R.A.H.M.S. database](https://brahms.ircam.fr/en/). See [the live
application](https://brahmsexplorer.onrender.com).

{{< image src="/images/brahms/ui.png" 
alt="The mobile and desktop user interface"
caption="The mobile and desktop user interface">}}

## Architecture

The application is built using a modular microservices architecture. The user
interacts with a frontend built with React. All the data that the frontend
needs, it gets via the backend, a Flask application responsible for
communicating between the frontend and the database. Both front and backends
are hosted on Render.

{{< image src="/images/brahms/brahms-diagram.en.png" 
alt="A diagram showing the relationship between the microservices"
caption="Relationship between the services">}}

The sync service, a Flask application also hosted on Render, scrapes IRCAM's
website for new pieces and updates the PlanetScale-hosted database accordingly.
This service is activated by a cron job hosted on Vercel, which hits an
API-endpoint to start the synchronisation.

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
