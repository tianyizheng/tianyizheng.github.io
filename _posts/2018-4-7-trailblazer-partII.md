---
layout: post
comments: true
title:  "Finding The Most Interesting Trail - Part II"
date:   2018-4-07
excerpt: "Trailblazer - helping you make the most out of the ourdoors - Part II"
image: "/images/trailblazer.jpg"
---

## Model and View

So last time I talked about the purpose of this webapp and not much else. In this post I'd like to talk about the core algorithm behind Trailblazer. To give you some background, the area we are querrying comes from the frontend in a Restful Json format. It specifies several values: `currentLatitude`, `currentLongitude`, `minimumTravelingDistance`, `maximumTravelingDistance`, `pointofInterestWeight` and `trackWeight`. These are user input values coming from the React+Redux powered frontend, which by the way was developed by my very talented teammate, Alexandre Palo.

Now after getting these parameters, we do a query to the remote Mongodb database. Using mongoengine as document mapper, our data object model looked like this: 

{% highlight python %}
class ChamberyRoads(Document):
  geometry = LineStringField()
  meta = {'strict': False}
{% endhighlight %}

To querry, simply do :
{% highlight python %}
allroad = ChamberyRoads.objects(geometry__near=[lon,lat], geometry__max_distance=maxDis)
{% endhighlight %}

This will give you all the roads near the user's current vincinity. 

## MongoDB
Being a document based NoSQL database, MongoDB is really good at geospatial Big Data querrying. If you are interested in why we chose mongoDB over other DBMS, I'd recommend reading the paper titled **Evaluation of Data Management Systems for Geospatial Big Data** by B. Murganete et al. and **NoSQL Databases: MongoDB vs Cassandra** by Veronika Abramova. But to give you some idea, NoSQL, as the name suggests, uses a schemaless design, and are designed to deal with large amounts of semi or unstructured data and are designed with a greater emphasis on horinzontal scalability and distribution of data. MongoDB is a document baed NoSQL database. You can read more on how MongoDB defines documents, but to our purpose let's jsut say it has better support for geospatial data, especially since it can store geospatial data using GeoJSON format, which were the one we used during data scrapings and post processings. 

Besides blazing fast querrying speed for geo spatial data, another reason to choose MongoDB for this project was the similar syntax it uses compared to Django and Python so it was easier for me to pick up.

## Path Finding

{: .image.fit}
![pathFinding](/images/pathFinding.png)

This algorithm is a modified version of Dijkstra inspired by random walks to address the need of finding the best weighted path within a certain distance, **without a known destination in mind**. We determined that cycles are okay(quite common for trails actually) if they are only traveled onece. 

We defined the best paths as 'ways' and this is the method: 
{% highlight python %}
way = getPath(allroad, (lon,lat), poiWeight, trackWeight, minDis, maxDis)
{% endhighlight %}

As you can see, we are passing in the `allroad` object into our function. MongoDB	returns geospatial data in geoJSON format. A "road" in this case would be a list of points, and each point is a tuple of longitude and latitude information, in addition to our preprocessed weights for poi and tracks. So keeping that in mind, we first need to build a graph. I used the NetworkX and GeoHash package for this purpose. GeoHash basically takes in longitude and latitude information and returns a unique hashed string. So for each road returned, we will hash its points, add it to a temporary dictionary so we can make sure we are not adding the same points twice in building our graph, and add an edge between the current and previous node. At the same time, we will also be keeping track of the closest node on the map to the user's current position, so we know where the starting point is. 

An important thing to keep in mind is that we will be keeping track of visited edges, instead of nodes for our purpose. The actual path finding works like this. Starting from the startnode, we look at its connected edges and go to the unvisited one with the best weight. If such edge exist, we check if we have visited this bestNode before. Here we introduct a previousDict that tracks the visited edges for a certain node. If the bestNode has a previous node, then we append the current node to the visited edges. If it doesn't, then we add an entry to the dictionary. Obviously now we need to visit the bestNode and add the edge to the list of paths. In the contrary, if we have visited all edges of current node, then it means we are at a deadend and needs to go back. We use the previousDict and traverse back one node at a time, performing the same algorithm, until we reach the desired length.

During the above traversing, we also keep track of the best paths' lengths and weighted score. We then pass this back to a serializer in Django and voila, we've got a list of optimal paths!
 
{: .image.fit}
![UI](/images/trailblazer_ux.png)

Here's the above info in code:

<script src="https://gist.github.com/tianyizheng/35a0d475b8d7ae8df2263efbe06ed393.js"></script>


Obviously this algorithm is quite slow, but I think it works pretty well given our constraints. It's slow because it needs a lot of computation. To perform graph traversal, we first need to parse all geoJSON so that we know which edge is best. (An alternative would be to query a smaller subset of data, using the same algorithm for path finding but checking if the currentNode has reached the edge of known area. However this requires more querries and the triguered query would be tricky in that we would like to avoid querying the same information over). This algorithm also cannot guarantee that it will result in the absolute best path in the area, since there is no way of knowing what lies ahead of the currentNode's immediate connected edges.

However, I do think there is still area for improvements. First of all the system architecture can be improved greatly by implementing distributed networks and using Redis with pub/sub. Second, we could change the entire system and simply present the best weighted paths in the user's near vincinety, similar to how google maps highlights traffic data. This way it would be intuitive to figure out where a possible destination might be and we can then simply use A* for shortest path finding. Another idea that was inspired by recent reading on the paper titled **DeepNav: Learning to Navigate Large Cities** by Samarth Brahmbhatt and James Hays, would be to train a neural net model to understand the semantics of the current surroundings. Say the user is currently in a business district, then the model can predict nearby park/nature resort destinations and compute paths that way.

Anyway, thanks for reading. Needless to say, I like graphing algorithms and if you have comments on this topic, feel free to reach out. 