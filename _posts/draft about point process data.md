---
title: 'Toronto Airbnb Spatial Point Pattern Exploratory Analysis'
date: 2026-06-14
permalink: /posts/2026/06/point-pattern-analysis/
tags:
  - statistics
  - academic
---

- intro to point pattern data and spatial data
- intro to toronto airbnb data
- what we expect to find from the data based on the variables
- testing for clusters
  - hypothesis testing
  - the tests we use, formulas for the tests
- 


Spatial data is unlike the data you would come across in finance and most other statistical analysis. The level of autocorrelation is far greater than whatever a "standard" level is, which follows a fundamental law of geography stated by Waldo Tobler in 1970: "*Everything is related to everything else, but near things are more related than distant things.*" Keeping this in mind, any modelling on spatial data cannot follow standard procedure of assuming observations are identically and independently distributed (IID). This creates some complications which I will not delve into here, but likely in a later writing.

For my first venture into spatial data, I chose to focus on point pattern data, where the location of the data is the primary interest. The following exploratory analysis focuses on Airbnb data in Toronto from January 16th, 2026. The data came from this site: [Airbnb Data](https://insideairbnb.com/get-the-data/)

After downloading and cleaning the data, we set the CRS (Coordinate Reference System) to the Canadian EPSG. This is important to contextualize our findings and put them in units that make sense for our data.

```{python}
torair = torair.to_crs("EPSG:3348")
```

Focusing on the room_type variable, below is the point pattern data for Toronto Airbnb listings

![Exploring Room Type](/images/cartodb_rooms_to.png)

Just from the above map, we see how there are more listings in the downtown and midtown cores as there is quite a bit of overlap. Towards the edges of the city, listings are already dispersing. This isn't enough, we need to look at the data from a statistical standpoint and really show that it is behaving a certain way. To better illustrate how listings may be acting certain ways across the city, we can form a histogram along both axes.

![Histogram x and y](/images/scatter_listings.png)
