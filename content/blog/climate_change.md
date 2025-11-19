---
title: "DIY: How to check that global warming is real"
date: 2020-10-18T17:28:50+01:00
draft: false
---
<style type="text/css">
  .row {
    display: flex;
    justify-content: space-between;
  }
</style>


I've never done any data science but today I had a chance to watch a few talks from the <a href="http://www.compsust.net/compsust-2020/">2020 Computational Sustainability Conference</a> and felt inspired to checkout the Python pandas package and see what I can do with it.


Looking for interesting sources of data I stumbled upon the National Oceanic and Atmospheric Administration website that allows you to download (for free!) historical weather data (<a href="https://www.ncdc.noaa.gov/cdo-web/search">https://www.ncdc.noaa.gov/cdo-web/search</a>) for many different locations around the world. Some of the data sets go as far as early XX century. How cool!


I quickly went ahead and downloaded the data for San Francisco.

Then the coding began…
It seems like to do any kind of data analysis using Python you need to install following packages:
<ul>
<li>numpy</li>
<li>pandas</li>
<li>matplotlib</li>
<li>seaborn</li>
</ul>

First step requires loading the data and seeing what it looks like
<img src="https://i.imgur.com/t8Ds2Vm.png" >

Unfortunately some of the rows were missing data for the daily average temperature in the TAVG column. So I had to apply a function to calculate the averages from min and max temperature values.

<img src="https://i.imgur.com/gJvAGBu.png" >
Much better!<br><br>

As the next step, I added a new column YEAR so that we can aggregate the values year by year. Then, I calculated the average temperature for each year using groupby()and mean() functions. I used the rolling average function to slightly smooth out the graph of average temperatures. Finally, I used the exponential weighted function to get a smooth line, that would (hopefully) show a general trend of the temperature changes.
<img src="https://i.imgur.com/qDJzkDz.png">

And that's it! Now we just need to plot the data…

<img src="https://i.imgur.com/K8Eo7Pt.png">

The results are pretty cool... Here's the graph for San Francisco:
<img src="https://i.imgur.com/gxg3Mz2.png">

I was so amazed with how clearly you can see the steady increase of the temperatures. Between 1965 and 2019 the yearly average temperatures grew from 13°C to 15.5°C. That's a 2.5°C increase! I quickly went ahead and downloaded data for other cities and ran my code for them too.

Here's New York City with data from 1919 to 2019:
<img src="https://i.imgur.com/CXr50e7.png">

In 1926 the average temperature was 10.74°C and today it is 13.18°C.<br><br>
Finally I wanted to check a non American city for comparison so here's the graph for London:
<img src="https://i.imgur.com/BxEsCfH.png">

The temperatures grew from 9.37°C in 1963 to 12.5°C in 2018. That's more than 3°C increase!<br>

Obviously there are many factors that contribute to an increase in temperature in cities. Over the years there are more and more people, more cars, more buildings, more everything. Bear in mind that I'm just playing with some datasets here. Best if you listen to the real scientists: <a href="https://climate.nasa.gov/news/2878/a-degree-of-concern-why-global-temperatures-matter/">https://climate.nasa.gov/news/2878/a-degree-of-concern-why-global-temperatures-matter/</a> 🙂

In the future I'd like to incorporate data on extreme weather events, population growth, gas emissions, etc to this project. Could also use some ML models for temperature predictions. Stay tuned!

Full code can be found on GitHub <a href="https://github.com/magda-getaround/global_warming">here</a>.
