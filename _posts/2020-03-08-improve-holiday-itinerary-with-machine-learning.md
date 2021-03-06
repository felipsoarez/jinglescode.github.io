---
title: How to Improve Holiday Itinerary with Machine Learning
layout: note
image: /assets/img/posts/improve-holiday-itinerary-with-machine-learning-01.jpg
image-thumb: /assets/img/posts/improve-holiday-itinerary-with-machine-learning-01-mini.jpg
description: Perfect your travel plans with this kick-ass strategy - saving time, effort and money!
tags:
- experiences
- visualisation
- algorithm
---

In October 2019, my friends and I are planning our awesome holiday in [Greece and Amalfi](https://tmpdrive.google.com/open?id=1klLY2yu88v6EpUkSP_vuOD4j1Dqi2ZHr&usp=sharing). The way we plan our perfect holiday itinerary is by using Google Map. We search for the places to visit and pin markers on Google Map for attractions that might interest us.

<iframe src="https://www.google.com/maps/d/u/0/embed?mid=1klLY2yu88v6EpUkSP_vuOD4j1Dqi2ZHr" width="100%" height="480"></iframe>

For me, to optimise our vacation to the fullest, as to cover as much ground as possible; I have three questions:
- How many days should I spend at one location?
- Which attractions/locations should I visit each day?
- What is the most optimal route from a place to another?

Let me show you the itinerary my algorithms have proposed for us. It is recommending the best sequence of places to visit, day by day.

{% include figure.html
  file="/assets/img/posts/improve-holiday-itinerary-with-machine-learning-02.gif"
%}

-   Start from  **Athens**, visit the Acropolis and other historic archaeological sites. That’s a great start!
-   Travel to  **Santorini**, a beautiful island with whitewashed homes and picture-perfect sunsets.
-   Sail to  **Crete**, for a road trip adventure of a lifetime, by exploring hidden corners and tasting the best of Greek cuisine.
-   Fly to Italy and travel to  **Amalfi**  and  **Ravello**  where the terraces are blending along the cliffs with the most spectacular coastal scenery.
-   Travel to  **Sorrento**  and  **Naples**, indulge in the best pasta, pizza, limoncello, and gelato.

Great holiday itinerary isn’t it?! This sequence of locations is fully generated by the algorithm. Would you like to try this for your next holiday? I have the  [codes](https://gist.github.com/jinglescode/418a7ef75471dd891ae8621f36db6b62)  ready for you.

In this article, I am going to show you exactly how you can do it too for your next vacation!

{% include figure.html
  file="/assets/img/posts/improve-holiday-itinerary-with-machine-learning-03.jpg"
  caption="View of Atrani, while hiking to Ravello [photo by Hong Jing (Jingles)]"
%}

# Preparation

**Pin locations on Google Maps.** Visit [My Maps](https://www.google.com/maps/d/u/0/home) and create a new map for your next holiday destination. Look for places you want to visit. Attractions? Theme parks? Restaurants? Fill up your map with markers by searching for these places and “_Add to map_”. Keep doing this until you have all the places you want to visit on the map.  _I will wait for you._

{% include figure.html
  file1="/assets/img/posts/improve-holiday-itinerary-with-machine-learning-04.jpg"
  file2="/assets/img/posts/improve-holiday-itinerary-with-machine-learning-05.jpg"
  size="c2"
%}

**Export map and upload into Colab.** After all the hard work of researching and pinning places to visit, we are ready to export the map. Select “_Export to KML/KMZ_”, and remember to check “_Export as KML_”. You will download a KML file.

{% include figure.html
  file1="/assets/img/posts/improve-holiday-itinerary-with-machine-learning-06.jpg"
  file2="/assets/img/posts/improve-holiday-itinerary-with-machine-learning-07.jpg"
  file3="/assets/img/posts/improve-holiday-itinerary-with-machine-learning-08.jpg"
  caption="Export KML file from  [Google Maps](https://www.google.com/maps/d/u/0/home)  and upload it in  [Colab](https://colab.research.google.com/)"
  size="c3"
%}

Next, go to  [Colab](https://colab.research.google.com/), an excellent Notebook environment maintained by Google. Open the File drawer on the left and upload the KML file which you have downloaded. We will be loading this KML file with  [BeautifulSoup](https://pypi.org/project/beautifulsoup4/)  later.

**Get the Google API key.** We will be plotting markers on Google Maps, so we need an API key. You can get it from the  [developers API page](https://developers.google.com/maps/documentation/javascript/get-api-key). Follow the instructions and you should get a key that looks like this:

```
ZIzkSyDwhp5B0_tH1$Is@fAkeAp1keY3eLYmPLY
```

_Psst: this is a fake API key_ 🙃

{% include figure.html
  file="/assets/img/posts/improve-holiday-itinerary-with-machine-learning-09.jpg"
  caption="Amalfi, Italy [photo by  [Tan Ying Ying](https://medium.com/u/f11516ac310b)]"
%}

# Code walkthrough on Colab

You can get the  [codes](https://gist.github.com/jinglescode/418a7ef75471dd891ae8621f36db6b62)  and run it on Colab.

**Define the parameters.** Let’s set the API key, KML filename, and the desired number of days for your long-awaited holiday.

The API key is for plotting interactive Google Maps on Colab. The KML file contains the places of interests you have pinned on Google Maps. And lastly, the algorithm will determine where you should visit for each day based on the number of days you have set.

{% include figure.html
  file="/assets/img/posts/improve-holiday-itinerary-with-machine-learning-10.jpg"
  size="s"
%}

**Load the data.** KML files are XML, and we can parse it with  [BeautifulSoup](https://pypi.org/project/beautifulsoup4/). Our places of interests are inside the “_Placemark_” tag, so we will extract the “_name_”, and “_coordinates_” from each “_Placemark_”.

<script src="https://gist.github.com/jinglescode/cc0cc5872eb46eca9660069e2190a1aa.js"></script>

Let’s see if the DataFrame contains places we are planning to visit.

<script src="https://gist.github.com/jinglescode/540b94b521105ddfab78a4c45e1daa9e.js"></script>

**Group locations by proximity.** With the coordinates for each location in the DataFrame, we can group them into clusters. If two places are near each other, they will be in the same cluster. There are a few methods for clustering, I will introduce K-Means, spectral and mean-shift clustering.

[**K-Means clustering**](https://scikit-learn.org/stable/modules/generated/sklearn.cluster.KMeans.html)  aims to partition data points into a specified number of clusters. In which each data point belongs to the cluster that it is nearest to.

[**Spectral clustering**](https://scikit-learn.org/stable/modules/generated/sklearn.cluster.SpectralClustering.html)  is useful when the structure of the individual clusters is highly non-convex. It performs dimensionality reduction before clustering in fewer dimensions.

[**Mean shift clustering**](https://scikit-learn.org/stable/modules/generated/sklearn.cluster.MeanShift.html)  is a centroid-based algorithm which aims to discover blobs in a smooth density of data points. It works by updating candidates for centroids to be the mean of the points within a region.

<script src="https://gist.github.com/jinglescode/875bc783890e89236d445f74bf8ab696.js"></script>

Which one did I end up using?  **K-Means**. Because it is straight forward, and the most common one.

**Plot it on Google Maps!** Now, we are ready to plot it on Google Maps to visualise the 12 main areas (because we are planning a holiday for 12 days). If you are running this on  [Colab](https://gist.github.com/jinglescode/418a7ef75471dd891ae8621f36db6b62), this map is interactive.

{% include figure.html
  file1="/assets/img/posts/improve-holiday-itinerary-with-machine-learning-11.jpg"
  file2="/assets/img/posts/improve-holiday-itinerary-with-machine-learning-12.jpg"
  file3="/assets/img/posts/improve-holiday-itinerary-with-machine-learning-13.jpg"
  caption="Athens. Santorini and Crete. Amalfi and Naples."
  size="c3"
%}

The clustering algorithm did a great job grouping locations by proximity. Next, I am interested in finding out the most optimal route from a region to another region.

**Find the middle of each cluster.** We need to find the middle point of each cluster. We do this with  [Pandas](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.groupby.html)  `groupby`  function:  
```
places.groupby(‘cluster’).mean()
```

That will give us a DataFrame, where each row represents a cluster of locations, and it’s latitude and longitude.

<script src="https://gist.github.com/jinglescode/0325f6715a60f940255c27c06b64483d.js"></script>

**Find the distance between cluster.** We need to find the distance between each cluster. We use the middle point of each cluster and calculate the distance between clusters with  [Scipy](https://docs.scipy.org/doc/scipy/reference/generated/scipy.spatial.distance.cdist.html)  `cdist`  function.

<script src="https://gist.github.com/jinglescode/5fc14023f48a08906ec2cd1573208c2d.js"></script>

That will give us a DataFrame, with the distance of each cluster to every other cluster. We will call this matrix the  **distance matrix**. Each value represents the distance between two clusters. A smaller value means the cluster is closer to each other.

<script src="https://gist.github.com/jinglescode/48b141054b371e3271d45fe283d9926e.js"></script>

**Find the shortest route.** With that distance matrix, we are ready to find the shortest path. Here is the code to compute the shortest path.

<script src="https://gist.github.com/jinglescode/96ed281c7ec0500ec67a1a7d8f4c8c9e.js"></script>

Initially, I was using the  [travelling salesman algorithm](https://en.wikipedia.org/wiki/Travelling_salesman_problem), but I find that the  [code’s](https://developers.google.com/optimization/routing/tsp)  a little overkill and overcomplicated, so I wrote my own instead. This is the result based on the  **shortest path algorithm**, a sequence from cluster to cluster.

```
2 -> 1 -> 6 -> 10 -> 9 -> 3 -> 11 -> 7 -> 4 -> 5 -> 8 -> 0
```

**Show the recommended itinerary.** Now we are ready to plot our markers on Google Map. It has recommended us to go from  **Athens**,  **Santorini**, and  **Crete**. Then to  **Amalfi**,  **Ravello**,  **Sorrento**  and  **Naples**. If you are running this on  [Colab](https://gist.github.com/jinglescode/418a7ef75471dd891ae8621f36db6b62), this map is interactive.

{% include figure.html
  file1="/assets/img/posts/improve-holiday-itinerary-with-machine-learning-14.jpg"
  file2="/assets/img/posts/improve-holiday-itinerary-with-machine-learning-15.jpg"
  file3="/assets/img/posts/improve-holiday-itinerary-with-machine-learning-16.jpg"
  caption="Start with Athens, Santorini, and then Crete. To Amalfi, Ravello, Sorrento and Naples."
  size="c3"
%}
{% include figure.html
  file1="/assets/img/posts/improve-holiday-itinerary-with-machine-learning-17.jpg"
  file2="/assets/img/posts/improve-holiday-itinerary-with-machine-learning-18.jpg"
  caption="Start with Athens, Santorini, and then Crete. To Amalfi, Ravello, Sorrento and Naples."
  size="c2"
%}

If you prefer to see it in  [tabular form](https://gist.github.com/jinglescode/f7a89cb57d71d84ace806a9f2cbaf340), we can display it with Pandas.

```
pd.set_option(‘display.max_rows’, None)  
places.sort_values(by=[‘days’])
```

{% include figure.html
  file="/assets/img/posts/improve-holiday-itinerary-with-machine-learning-19.jpg"
  caption="Oia, Santorini, Greece [photo by [Clement Lim](https://medium.com/u/4455aa7806f7)]"
%}

# What can be improved?

This algorithm does not take the number of attractions in one region into consideration. For instance, we absolutely didn’t want to explore everything in Athens in one day! Neither is just one day at Santorini enough.

We need a type of clustering algorithm that constraint on the maximum number of points in a cluster. Such that, every cluster should have about the same number of locations. If a cluster is becoming too dense, it will split the cluster into two or more clusters.

But if you are travelling in one city, the result can be promising. This is the generated itinerary for New York City. Feel free to download my  [itinerary](https://drive.google.com/open?id=1SSy8rwXf-Z7UKwPT1Jv_yZDNisHqR3H2&usp=sharing)  and try it for yourself.

{% include figure.html
  file="/assets/img/posts/improve-holiday-itinerary-with-machine-learning-20.jpg"
  caption="[New York City](https://drive.google.com/open?id=1SSy8rwXf-Z7UKwPT1Jv_yZDNisHqR3H2&usp=sharing)"
  size="m"
%}

{% include figure.html
  file="/assets/img/posts/improve-holiday-itinerary-with-machine-learning-21.jpg"
  caption="New York City [photo by  [Hong Jing (Jingles)](https://medium.com/u/641197e9ee36)]"
%}

----------

# Resources

Do you want to try this for your next vacation? Here’s [the code](https://gist.github.com/jinglescode/418a7ef75471dd891ae8621f36db6b62). _You’re welcome._ 😉

<script src="https://gist.github.com/jinglescode/418a7ef75471dd891ae8621f36db6b62.js"></script>

Not sure where to visit in Greece or Amalfi? Go ahead and use our  [Greece x Amalfi itinerary](https://drive.google.com/open?id=1klLY2yu88v6EpUkSP_vuOD4j1Dqi2ZHr&usp=sharing).

<iframe src="https://www.google.com/maps/d/u/0/embed?mid=1klLY2yu88v6EpUkSP_vuOD4j1Dqi2ZHr" width="100%" height="480"></iframe>

Not sure where to visit in  [New York City](https://drive.google.com/open?id=1SSy8rwXf-Z7UKwPT1Jv_yZDNisHqR3H2&usp=sharing)?

<iframe src="https://www.google.com/maps/d/embed?mid=1SSy8rwXf-Z7UKwPT1Jv_yZDNisHqR3H2" width="100%" height="480"></iframe>

Thanks for reading. 😃  
Hope you enjoyed reading this article as much as I did preparing it.
