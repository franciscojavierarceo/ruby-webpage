---
layout: post
title: 'A measly critique'
---

During the first week of February, [Danny Sheehan](http://www.github/nygeog) and I were pitching around a few ideas for datasets that would be worth looking at and utilizing for a capstone demo for our [in-class presentation on mapping in D3.js](http://stat4701-edav-d3.github.io/). We both recalled these [terrific visualizations](http://www.nytimes.com/interactive/2015/02/02/us/measles-facts.html) by Johnathan Corum, Josh Keller, Haeyoun Park, and Archie Tse of the New York Times that illustrated the severity and distribution of measles cases in the United States for this year:

![cases map]({{ stephenrra.github.io }}/images/cases-map-1050.png "NYT Measles Viz")
**Source**: "Facts About The Measles Outbreak" <cite>The New York Times</cite>, Feb 2, 2015

The timing of these visualizations was more or less perfect. They are, in some ways, an elegant data-driven riposte to the current [anti-vaxxer zeitgeist](http://news.nationalgeographic.com/news/2015/02/150206-measles-vaccine-disney-outbreak-polio-health-science-infocus/) by reflecting the unusually high number of measles cases this year: the numbers of cases in January alone were higher than many of the annual case totals for the years since 2000. Having read that this year's flu vaccine has only been [23% effective](http://www.nytimes.com/aponline/2015/01/15/health/ap-us-med-flu-vaccine.html) (in part due to a failure to predict the H3N2 strain of the virus) and wanting to do something in a similar vein (<s>no</s> OK, pun intended) of public health, we decided to use Google's interesting, if [flawed](http://www.sciencemag.org/content/343/6176/1203.full), Flu Trends data to motivate a [dynamic visualization of city flu trends in the United States](http://stat4701-edav-d3.github.io/viz/cities/cities.html). 

### The Visualization

Given all this media attention in the last year to the Ebola outbreak in West Africa as well as MERS and the flu and measles cases, I thought this post would be a great opportunity to revisit the lauded [Wall Street Journal's measles visualization](http://graphics.wsj.com/infectious-diseases-and-vaccines/) from the mini-assignment earlier in the semester and see what what kind of improvements or tweaks could be added to it.

![original viz]({{ stephenrra.github.io }}/images/wsjmeasles.png)
**Source**: "Battling Infectious Diseases in the 20th Century (Tycho Project CDC Data)" <cite>The Wall Street Journal</cite>, Feb 11, 2015

### The Critique

Given the popularity and exposure of the WSJ's sets of infectious diseases visualizations and having made its rounds on the interwebs, it comes as little surprise that remaking these graphics has not exactly been *terra incognita*. For example, [Robert Allison](http://robslink.com/), a notable expert in viz using SAS/GRAPH, recreated the [plot](http://blogs.sas.com/content/sastraining/2015/02/17/how-to-make-infectious-diseases-look-better/?utm_source=feedburner&utm_medium=feed&utm_campaign=Feed%3A+sasblogs+%28SAS+Blogs%29). And it has since had the inevitable remake [using ggplot2](http://www.r-bloggers.com/recreating-the-vaccination-heatmaps-in-r/)). While everyone is queuing up with their own take, I wanted to point out a few of the issues I've had with the original as well as subsequent visualizations within a Tufte-esque framework:

1. <strong> The labels.</strong> First, and as I recall looking at briefly in class, is the lack of complete state labels along the left edge (only 17 state names are present). The lack of room for all the labels could certainly be addressed fair easily, in part, with abbreviations of state names.

2. <strong> Data-ink and chart junk.</strong> While there is an awful lot of information being crammed into this visualization, the original graphic and subsequent recreations have all preserved a feature that has bothered me from the outset -- the color progression/ramping of the data. From an intuitive standpoint, I felt this was particularly distracting since the ramping of the color was not necessarily obvious (a false color scheme) and I found myself referring to the gradient key more than I would have liked in looking at the data -- red to orange to yellow to blue and then low saturation. Seems fairly arbitrary. I was curious about delving more into this phenomenon and stumbled upon [this enlightening blogpost](http://blog.visual.ly/subtleties-of-color-the-perfect-palette/) by Robert Simmon, a visual designer, on the subtleties of color interaction, palette, and perception. One particular excerpt seemed apropos to mention here:

>"A palette should also minimize errors from the color shifts introduced by nearby areas of differing color or lightness, a phenomenon known as simultaneous contrast."

3. <strong> Simultaneous contrast.</strong> Going a bit further, I found that simultaneous contrast in visual effects occurs when the appearance of a patch of light (the "test field") is affected by other light patches ("inducing fields") that are nearby in space and time, respectively. This is notoriously tricky to get right in dynamic graphics, e.g., moving maps, in which symbols move from one background color to another. In the interactive WSJ viz, the simultaneous contrast effect seems to be compounded by the mouse hover where each data box immediately turns a light blue color which could be slightly confusing for some. I aim to change the rainbowish ramping of the original viz and ggplot2 remake so as to be less of a jarring simultaneous contrast transition for the reader. This improvement ought to be more along the lines of the SAS remake where the color progression is along a gradient of one color (red). Hopefully this promotes more focus on the substance, less focus on other stuff.

4. <strong> Graphical integrity (?).</strong> Lastly, one of the main things that bugged me about the original visualization (and one that has only been slightly mitigated in subsequent attempts) is the difficulty I had in distinguishing between missing data (NA) and zero values. As the Tycho Project [points out](http://www.tycho.pitt.edu/faq.php), there is indeed a difference:

> "The '-' value indicates that there is no data for that particular week, disease, and location. The '0' value indicates a report of zero cases or deaths for that particular week, disease, and location."

I'm not implying that this is in anyway some sort of deliberate data obfuscation but the almost imperceptibly light gray color of missing data and the light purple/blue of the zero values makes interpretation that much more difficult. 

### The Tweak

The Tycho data is publicly accessible from [here](http://www.tycho.pitt.edu/l1advanced.php). I'll be also using the the enhanced heatmaps (```heatmaps.2()```) function from the ```gplots``` package.

Diving in then, here are the preliminaries:

```{r}

require(gplots)
require(RColorBrewer)
setwd('~/Dropbox/Documents/Academics/Courses/Git/stephenrra.github.io/data/')
measles <- read.csv('MEASLES_Incidence_1928-2003_20150403094837.csv', stringsAsFactors = F)

```
Having read in the data, I can see that the data has been organized by weeks:

![measles data by week]({{ stephenrra.github.io }}/images/weekdata.png)

Here, I want to calculate the average across a given year without consideration of the NA values. I wanted to purposefully avoid taking a yearly sum since simply summing the weekly data for a given year might induce a missing data bias that may be an under-reporting of the actual annual value. Therefore, I wanted to take the empirical mean and then multiply by 52 weeks to get the expected number of cases per 100,000 individuals per year.

```{r}
# Replace missing values 
measles[measles == "-"] <- NA
for (i in 2:NCOL(measles)) {
  measles[, i] <- as.numeric(measles[, i])
}

# Taking the annual mean 
measles <- measles[measles$YEAR>=1930,]
y <- aggregate(measles[,3:NCOL(measles)], by=list(year=measles[,1]), function(x) 52*mean(x, na.rm = TRUE))

for (i in 1:NCOL(y)) {
  y[is.nan(y[, i]), i] <- NA
}

y <- y[order(y$year),]

# Row labels by decade
row.labels <- rep("", 72)
row.labels[c(1,11,21,31,41,51,61,71)] <- c("1930","1940","1950","1960","1970",
                                           "1980","1990","2000")
# Initial color ramp palette 
cols <- colorRampPalette(c("red", "blue"))(100)
bks <- seq(0, max(y[, -1], na.rm = TRUE), length.out = 101)
par(cex.main=0.8)

# Initial heatmap
heatmap.2(as.matrix(t(y[,2:NCOL(y)])), Rowv=NULL, Colv=NULL, 
          dendrogram="none", trace="none", key=FALSE,
          labCol=row.labels, cexCol=1, lhei=c(0.15,1), lwid=c(0.1,1), margins=c(5,12),
          col=cols, breaks=bks, colsep=1:72, srtCol=0, rowsep=1:57, sepcolor="white",
          add.expr=lines(c(32,32),c(0,1000),lwd=2),
          main='Measles Incidence in US States (1930-2003)\nVaccine introduced 1961
             \n(data from Project Tycho)')
```

And here's what I got as an initial heatmap:

![heatmap v1]({{ stephenrra.github.io }}/images/heatmap_v1.png)

Yikes....well, at least the NA value color has now been converted to white. The color progression is how I initially wanted it to be (red). Lots of problems still. So what else could I do at this point?
Well, there's still the issue of the margins and the state names. All the state names show up this time (unlike the WSJ graphic) but it's far too compact and dense. Here, I downloaded a simple .csv of the
[abbreviations of all 50 U.S. states](http://www.50states.com/abbreviations.htm#.VSgJpXXd89Y) and read it in:

```{r}
# Reading in abbreviated state names file
stNames <- read.csv('stateNames.csv', header = FALSE, stringsAsFactors = FALSE)
names(y)[2:52] <- stNames[,2]
```

Next, I liked the idea of keeping the NA values white but perhaps have a progression that is less glaring and make a little more sense visually. I took a little bit of inspiration from the Burgoyne Diller-inspired exhibition poster on p. 185 of the Tufte book. In particular, I liked the simultaneous contrast (although the thin black lines are a neutral buffer) between beige and red and thought that perhaps I could fiddle with different color palettes until I got a color ramp with the most extreme values in a similar, albeit toned down, shade of red. Here's the excerpt from the Tufte book for reference:

![heatmap v2]({{ stephenrra.github.io }}/images/diller.png)

 Here, I'll call upon ```RBrewer```. This is a nifty
package in R that allows you to create all sorts of nice color schemes for thematic maps and such. A little playing around then with the ```brewer.pal``` function...

```{r}
cols <- colorRampPalette(brewer.pal(8, 'Reds'))(100)
bks <- seq(0, max(y[, -1], na.rm = TRUE), length.out = 101)

par(cex.main=0.8)
heatmap.2(as.matrix(t(y[,2:NCOL(y)])), Rowv=NULL, Colv=NULL, 
        dendrogram="none", trace="none", key=FALSE,
        labCol=row.labels, cexCol=1, lhei=c(0.15,1), lwid=c(0.1,1), margins=c(5,12),
        breaks=bks, colsep=1:72, srtCol=0, rowsep=1:57, sepcolor="white", col=cols, 
        add.expr=lines(c(32,32),c(0,1000),lwd=2),
        main='Measles Incidence in US States (1930-2003)\nVaccine introduced 1961', na.color = grey(0.8))

```
And now the second version of the heatmap:

![heatmap v2]({{ stephenrra.github.io }}/images/heatmap_v2.png)

So now what? Well, going back to square one briefly, I wanted to see if there was a better way of bucketing the data so I got this histogram to see what was going on underneath the hood:

```{r}
# Grouping together values into a 2500+ bucket
hist(as.matrix(y[,1:NCOL(y)]), xlab = "Measles cases per 100,000 people", main="Histogram of measles data")
```

![heatmap v2]({{ stephenrra.github.io }}/images/hist.png)

From this, I figured it might make more sense to establish a somewhat arbitrary cutoff of 2500 and just dump everything above 2500 into its own "2500 plus" category.

```{r}
y2 <- y[, 2:NCOL(y)]
sum(y2[!is.na(y2)] > 2500)

y2[y2 > 2500] <- 2500

cols <- colorRampPalette(brewer.pal(8, 'Reds'))(100)
bks <- seq(0, max(y2, na.rm = TRUE), length.out = 101)

par(cex.main=0.8)
heatmap.2(as.matrix(t(y2)), Rowv=NULL, Colv=NULL, 
        dendrogram="none", trace="none", key=FALSE,
        labCol=row.labels, cexCol=1, lhei=c(0.15,1), lwid=c(0.1,1), margins=c(5,12),
        breaks=bks, colsep=1:72, srtCol=0, rowsep=1:57, sepcolor="white", col=cols, 
        add.expr=lines(c(32,32),c(0,1000),lwd=2),
        main='Measles Incidence in US States (1930-2003)\nVaccine introduced 1961', na.color = grey(0.8))

```
And so we now get:

![heatmap v3]({{ stephenrra.github.io }}/images/heatmap_v3.png)


Lastly, and as we have talked about in previous class sessions, there's this kind of weird arbitrariness to ordering when it comes to these types of heatmaps. Just from an intuitive sense, I thought that it might make more sense to organize the data (the states) from top to bottom in order of mean number of cases per 100,000. Add a few minor tweaks to the plot itself and:


```{r}
means <- apply(y2, 2, function(x) mean(x, na.rm = TRUE))

y3 <- y2[, rev(order(means))]

par(cex.main=0.8)
heatmap.2(as.matrix(t(y3)), Rowv=NULL, Colv=NULL, 
          dendrogram="none", trace="none", key=FALSE,
          labCol=row.labels, cexCol=1, lhei=c(0.15,1), lwid=c(0.1,1), margins=c(5,12),
          breaks=bks, colsep=1:72, srtCol=0, rowsep=1:57, sepcolor="white", col=cols, 
          add.expr=lines(c(32,32),c(0,1000),lwd=2),
          main='Measles Incidence in US States (1930-2003)\nVaccine introduced 1961', na.color = grey(0.85))
```

And finally, voila:

![heatmap final]({{ stephenrra.github.io }}/images/heatmap_v4.png)

However, still need to adjust the key...

```{r}
y3 <- y2[, rev(order(means))]
lmat = rbind(c(0,3),c(2,1),c(0,4))
par(cex.main=0.8)
heatmap.2(as.matrix(t(y3)), Rowv=NULL, Colv=NULL, 
          dendrogram="none", trace="none", key=TRUE, keysize="1",
          labCol=row.labels, cexCol=1, lmat = lmat, lhei=c(0.5,4,1), lwid=c(0.1,1), margins=c(5,12),
          breaks=bks, colsep=1:72, srtCol=0, rowsep=1:57, sepcolor="white", col=cols, 
          add.expr=lines(c(32,32),c(0,1000),lwd=2),
          main='Measles Incidence in US States (1930-2003)', na.color = grey(0.85))
```

![heatmap final]({{ stephenrra.github.io }}/images/heatmap_v4key.png)
