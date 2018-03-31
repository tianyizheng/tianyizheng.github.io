---
layout: post
title:  "Finding The Most Interesting Trail"
date:   2018-3-30
excerpt: "Trailblazer - helping you make the most out of the ourdoors"
image: "/images/trailblazer.jpg"
---

## Background

If you are like me and feel like there is a lot of natural world around your area that you haven't explored yet, where would you go to look up that information? You could use Google maps, but that only gives you route to a know destination. You could go find some blogs that post about intersting trails, but going back and forth to collect this information quickly gets annoying. 

Trailblazer is a very intersting web app that I worked on for a data visualization class. It collects information from several differnet sources such as Geocahing, OpenStreetMap and Uttagawa to build a centered map around your area that has all the points of interests and exciting trails. The backend Django + MongoDB server then uses a Dijkstra based algorithm to quickly calcualtes the most interesting trails around you, using your current location as the starting point. You can also specify the amount of distance you would like to travel and how much you value POIs and trails! It is a whole new graph theory based route recommendation experience.

More information will come soon after I finish with the documentations