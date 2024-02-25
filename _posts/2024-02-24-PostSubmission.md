---
layout: post
title:  "How to make a Heatmap using R"
date: 2024-02-24
description: We will go over how to make a 2D heatmap of 2 numeric variables using R/RStudio.  
image: "/assets/img/heatmap.webp"
display_image: true  # change this to true to display the image below the banner 
---
<p class="intro"><span class="dropcap">W</span>elcome to this guide on how to make a heatmap in R. I originally did this as part of a project looking at police data from traffic stops across Nashville Tennessee. I hope this guide proves useful in understanding how to make an effective (and interesting) Heatmap.</p>

### Why make a heatmap?

Heatmaps are especially useful if you want to show the distribution of data across 2 variables. If you had 1 variable of interest, a boxplot, density plot, or barchart would be useful. Heatmaps help if you have a large data set with numerous levels of categorical variables or a wide range for continuous numerical variables.

For my project, we wanted to see what areas of Nashville had the most traffic stops. Our data contained coordinates in Latitude and Longitude for each stop. Our data set was extremely large (over 3.4 million observations) so a heatmap would work very well in showing which sets of coordinates showed up most frequently in our data.

---
---

### Useful Libraries  

Some of the libraries I used made it easier to create my heatmap. The libraries I recommend are:

1. 'tidyverse'
2. 'ggplot2'
3. Optional for background: 'png' or 'jpeg'

Interestingly enough, tidyverse and the base ggplot functions have most of the tools needed to create a really nice heatmap of your data.

Code:
\# Loading in libraries  
library(tidyverse)  
library(ggplot2)  
library(png)

---
---

### Cleaning our data and preparing for the heatmap  

Just like in any Data Science project, you want to make sure your data is clean and ready to work with. This means you want to run through a couple of steps:

1. Remove or fill in observations where your variable is missing.

2. Make sure the data you are using makes sense and is applicable.

3. If needed, format your data or data frame to be ready for plotting.

During our project, we simply removed any observations where the coordinates were not provided. This reduced the size of our data slightly. 

The next thing we did was filtered out the observations where the coordinates were outside of Nashville. We notcied some extreme values that were located in Las Vegas and New Jersey, and since these were not in our area of focus, we needed to remove them.

The last thing we did was we actually made a subset of our data that only included the coordinates for stops, since that was all the data we needed and we would be constructing our heatmap from the counts of each coordinate pair occurance. We then bunched the coordinates into manageable 'buckets' or ranges of values that we could plot. We did this by rounding each value to the hundreth place since each coordinate had 6 decimal places. Then, we added a count column that counted the number of occurences of each combination.

I would also recommend thinking about whether or not you want a heatmap of counts or one of probability/density. The default method is for counts, though density might be what you are looking for. If this is the case, be sure to format your data frame so that instead of counts, it calculates the probability for each section of the heatmap. We did a secondary heatmap with probabilities, which we did by taking the count of each combination and dividing that by the total number of stops and putting that in our 'Count" column which we renamed to 'Probability'

Code:
\# Cleaning Data  
clean_data <- data |>  
    drop_na(var1) |>  
    drop_na(var2) |>  
    filter(var1 < max_val_of_interest & var1 > min_val_of_interest)

\# Format data for plotting by count  
Heatmap_data <- clean_data |> select(var1, var2)  
Heatmap_data <- count(Heatmap_data, var1, var2)

\# Format data for plotting by density  
TotalCount <- sum(Heatmap_data$n)  
Heatmap_data_density <- Heatmap_data  
Heatmap_data_density$n <- (Heatmap_data_density$n / TotalCount)  
Heatmap_data_density <- Heatmap_data_density %>% rename(Prob = n)

---
---

### Plotting the heatmap

Now we get onto plotting our heatmap. I would recommend being familiar with ggplot. You can find a nice tutorial [here](https://r-statistics.co/Complete-Ggplot2-Tutorial-Part1-With-R-Code.html).

If you want to put a background image onto your heatmap, you can do so by using readPNG/readJPG depending on the file type using the appropriate library from the 'Useful Libraries' step. Save your image as a simple name so you can call it easily later.

The steps to plot the heatmap are fairly simple:

1. Start by using group_by() on your data frame and group it by BOTH of the variables you want to plot on your heatmap.

2. Start your ggplot(), using aes with the proper x and y values, and include fill=n for count or fill=Prob for density.

3. If you included a background image, use background_image(image_var_name) to include it in the plot.

4. Now you want to use geom_tile() to make your actual heatmap. Inside the parentheses, use alpha to change the opacity of your heatmap if you included a background image. I recommend testing different values to see which works best.

5. By default, heatmap will use a blue color scheme that maps from light-blue to dark-blue. If you want to change that, you can use scale_fill_viridis() to change the color. The documentation can be found [here](https://www.rdocumentation.org/packages/viridis/versions/0.6.5/topics/scale_fill_viridis).

6. Include theme, labels, limits, or anything else required for your graph.

### Admire Your Beautiful Heatmap

Congrats! You should have a wonderful heatmap that plots your data across two dimensions, being filled in by the count/density.

If you encounter problems, here are some simple solutions:

##### I can't see my background image
If this happens, be sure to set your alpha within the geom_tile() function

##### The plot doesn't have the right bounds or buckets
If this happens, you might need to adjust your buckets when you format your data. If you still have issues, be sure to try to remove unneccessary data, as well as missing values. If you still have bounds problems, try using xlim() and ylim() to force bounds on your heatmap.

##### My ggplot isn't working
Be sure to include + in between each function, and %>% after the group_by(). If you continue to have problems, check out [this cool help guide](https://ggplot2.tidyverse.org/).

---
---

### Try it yourself 

Now that you've gotten here, you should try to build your own heatmap. It's not as hard as it seems! Luckily ggplot has all the stuff you need, so find a fun data set and get to plotting!

Be sure to share the post with friends who you think might enjoy making their own heatmaps.

---
---