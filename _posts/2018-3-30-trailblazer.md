---
layout: post
comments: true
title:  "Finding The Most Interesting Trail"
date:   2018-3-30
excerpt: "Trailblazer - helping you make the most out of the ourdoors"
image: "/images/trailblazer.jpg"
---

## Background

If you are like me and feel like there is a lot of natural world around your area that you haven't explored yet, where would you go to look up that information? You could use Google maps, but that only gives you route to a know destination. You could go find some blogs that post about intersting trails, but going back and forth to collect this information quickly gets annoying. 

Trailblazer is a very intersting web app that I worked on for a data visualization class. It collects information from several differnet sources such as Geocahing, OpenStreetMap and Uttagawa to build a centered map around your area that has all the points of interests and exciting trails. The backend Django + MongoDB server then uses a Dijkstra based algorithm to quickly calcualtes the most interesting trails around you, using your current location as the starting point. You can also specify the amount of distance you would like to travel and how much you value POIs and trails! It is a whole new graph theory based route recommendation experience.

## Data

{: style="text-align:center"}
![Resnet](/images/scrapping_flow.png)

In addition to the publicly available OpenStreetMap data, we scrapped the data from two sites, Uttagawa and Geocache. Uttagawa gave us the trails in a specific area and Geocache gave us the points of interests. At the end we got three sets of data: 11,130 Utagawa trails, 246,984 Geocache POIs, and 354,883 roads from OpenStreetMap

For our purpose, we decided to preprocess the data so that it is easier for the path finding algorithm to work on. Each node on the OpenStreetmap was querried and compared to nearby POI and Uttagawa trails and assigned weights. We defined "nearby" as within 25m, since past surveys has found that consumer GPS has an error ranging from 3-7 meters. The weights used here is a combination of poiWeight and trackWeight, balanced with user preferences. 

## Frontend

The frontend was implemented with React and Redux. Since this is a single page application that requires lots of updates, React's components mechanism let us to compute the states of mechanism without refreshing the page, resulting in fast reloads. This part was mainly worked on by Alexandre Palo. In the next part, I will talk more about the backend and algorithm behind this application, which were designed and implemented by me. 