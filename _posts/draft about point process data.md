---
title: 'Toronto Airbnb Spatial Point Pattern Exploratory Analysis'
date: 2026-06-21
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

## Introduction to Spatial Point Patterns

Spatial data is unlike the data you would come across in finance and most other statistical analysis. The level of autocorrelation is far greater than whatever a "standard" level is, which follows a fundamental law of geography stated by Waldo Tobler in 1970: "*Everything is related to everything else, but near things are more related than distant things.*" Keeping this in mind, any modelling on spatial data cannot follow standard procedure of assuming observations are identically and independently distributed (IID). This creates some complications which I will not delve into here, but likely in a later writing.

For my first venture into spatial data, I am choosing to focus on point pattern data, where the location of the data is the primary interest. The following exploratory analysis focuses on Airbnb data in Toronto from January 16th, 2026. The data came from this site: [Airbnb Data](https://insideairbnb.com/get-the-data/)

After downloading and cleaning the data, we set the CRS (Coordinate Reference System) to the Canadian EPSG. This is important to contextualize our findings and put them in units that make sense for our data.

```{python}
torair = torair.to_crs("EPSG:3348")
```

Focusing on the room_type variable, below is the plotted point pattern data for Toronto Airbnb listings.

![Exploring Room Type](/images/cartodb_rooms_to.png)

Just from the above map, we see how there are more listings in the downtown and midtown cores as there is quite a bit of overlap. Towards the edges of the city, listings are already dispersing. 

## Visualization

The above explanation isn't enough, we need to look at the data from a statistical standpoint and really show that it is behaving a certain way. To better illustrate how listings may be acting certain ways across the city, we can form a histogram along both axes.

![Histogram x and y](/images/scatter_listings.png)

Our previous observations were confirmed. There is a higher concentration of listings in the downtown / midtown area compared to the edges of the city. However, there is a slightly higher concentration in the north end compared to the east and west ends. 

The histograms are great for evaluating overall trends, but it is difficult to pinpoint where density truly lies as you must line up the histograms perfectly. Instead, we can have it on the map itself using hexagonal binning. In this case, we are interested in the number of instances which fall into the hexagonal bins we overlay onto the map. There is a better sense of how listings fall around the city.

![Hex Binning](/images/hex_airbnb.png)

Again, we see how listings are concentrated in the downtown core, but there are bins that are dark in the north end as well. As best practice, I won't speculate further on why there are so many listings, I will take the data at face value. 

## Centrography

Rather than looking at the data in its raw form, we can create summary statistics such as mean, median, and weighted mean. These can be further subset by the variable type, such as listing type.

![Listing means](/images/listing_type_means.png)

The mean of the private rooms appears to be centralized in midtown, but the dispersion ellipse is also far bigger. This suggests there are plenty of private room listings in the Toronto region. Meanwhile, the mean of the entire home/apt listings is further south, with a dispersion ellipse smaller, which suggests more concentration in the downtown area. Based on housing trends in the city, this makes sense. There are more homes with entirely separable private rooms not in the downtown core, yet listings in the core would likely get more bookings. Meanwhile, an entire home / apt would likely mean a few rooms at most in a high-rise, which are solidly within the downtown area. Full houses have the flexibility to provide a single room while an apartment can only be entirely rented out. 

## Clustering 

In this section, we can identify cluster patterns using the Quadrat statistic and Ripley's G-test.

**Quadrat Statistic:**

$$\chi^2 = \sum_{i=1}^{m} \sum_{j=1}^{k} \frac{[x_{i,j} - E(x_{i,j})]^2}{\lambda |A_{i,j}|}$$

The quadrat statistic compares complete spatial randomness to the actual point process in the data. As the benchmark/null process, we would expect complete spatial randomness to have a uniform number of points per equally sized subsection of the map. Whether that be a grid, hexagon, or other shape is up to the data and researcher. 

Using the chi-squared test for this data, we arrived at a chi-squared value of 127082.646, and a p-value of the test at 0. For a practically all significance levels, this result rejects the null hypothesis. We can assert the plot is NOT completely spatially random. Below is a plot showing such case:

![Quadrat Statistic](/images/quadrat.png)

There is a caveat to the quadrat statistic, that being there are subsections with no values because it is out of the range of the plotted area. This makes our result skewed, so for our next test, we create a concave hull to contain our data AND test. 

**Ripley's G-test:**

We first create a concave hull below. 

![Concave hull](/images/concave_hull_abnb.png)

$$G(r) = \frac{1}{N} \sum^{N}_{i=1} I(d_i \leq r)$$

$$G_{0}(r) = 1 - e^{-\lambda \pi r^2}$$

Ripley's G-test compares the median ratio of nearest neighbours with distances shorter than some predetermined distance variable to the actual ratio of nearest neighbours within that distance. For example, the median simulated ratio of nearest neighbours within 50 meters may be 0.2. This serves as our null hypothesis/benchmark. Depending on the number of points in the sample/population, a sufficiently high ratio would be needed to reject the null hypothesis. Typically, a sufficiently high ratio within a relatively short distance suggests that clustering is present and the population / points are not homogenous. In the below plot, we simulate the median using monte-carlo.

![Ripley's G-test](/images/g_test_airbnb.png)

In this plot, we can see that the observed line above the median simulation suggests there is a far greater ratio of near neighbours than expected. Visually, this means points tend to cluster throughout the population. Relating back to the plot of points, this is especially true for the downtown core, but it is interesting to see it play out across the rest of the city too.

Trying to pin a specific distribution to the point process is beyond the scope of this analysis, but I am interested in seeing how it could be modelled with so many covariates being highly correlated with one another. What's next? I hope to look into more types of spatial data like areal or geostatistical, but those will be in a future post. The analysis of 
