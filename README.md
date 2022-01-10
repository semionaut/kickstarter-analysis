# An analysis of kickstarter campaigns

Analysis of kickstarter projects dataset 2009-2017

## Background & Purpose ##

The client is [Louise](https://courses.bootcampspot.com/courses/1023/pages/1-dot-0-4-kickstarting-with-excel?module_item_id=390096), a rad, up-and-coming playwright looking to start a crowdfunding campaign for her breakout new play, *Fever*. To help provide cogent recommedations that will give Louise the greatest chance of success when planning her project, I analyzed a dataset of past Kickstarter campaigns which took place between the years 2009-2017. Through a close look at the Kickstarter data, it is possible to determine the most optimal parameters that contribute to a **successful** crowdfunding effort; for example: funding expectations, time of year, duration, and breadth of promotion.

## Analysis & Challenges ##

In this section, I talk a little about the methodology for preparing and analyzing the data, grouped by the following:

* cleaning and preparing the data
* choosing the right data
* looking for clues
* challenges or potential problems

##### Data Cleaning #####

The raw data needed some sprucing up in order to be worked with easily. Part of preparing data for analysis involves ensuring that data follows standardized formats we can make use of. For starters, nothing in our dataset could work as a time series without first converting the campaign start and end dates from Unix epoch timestamp to human-readable datetime format. In order to convert epoch (seconds elapsed since Jan 1, 1970) from its numerical string, that can be done using the formula below. This allowed the values for 'Date Created' and 'Date Ended' to be populated in date format.

```
=(((J2/60)/60)/24)+DATE(1970,1,1)
```
![epoch](/assets/screenshots/epoch-datetime.png)

Where the specific day may not be as consequential, we later simplified these values into the 'Years' column using the `YEAR()` function.

In the same vein, the dataset was initially presented with 'Category and Subcategory' aggregated into a single column. Since Louise is crowdfunding a play, not only is it relevant to differentiate between say, film & video productions versus live theater, but it is also potentially relevant to be able to disaggregate the data between plays and the other categories of theater projects, musicals and spaces. Using the 'text to columns' function in Excel, it was possible to subdivide the entries for 'Category and Subcategory' into the data that populates the columns for 'Parent Category' and 'Subcategory'. Since it will be discussed again later, it should be noted here that the most common category of campaigns in this dataset were theater campaigns, with plays being the majority of all campaigns requested by subcategory.

##### Selecting Data #####

To perform an accurate analysis starts with validating and understanding the dataset. This was achieved by performing a statistical analysis calculating the standard deviation and interquartile range (IQT) to understand the range and bounds of the data as well as identify the presence of any outliers in the data that would affect, for example, calculations of mean funding amount.

Applying filters to the data allow slices to be selected according to different parameters which was helpful to drill down into the significance of different variables.

##### Looking For Clues #####

To be able to help Louise, it was possible to pinpoint those crieteria which might strongly correlate with the success or failure of a given crowdfunding campaign. For this a subset of the data containing relevant campaigns was distilled according to two variables, launch date (month of the year) and goal amount (total funding requested for the campaign)

To explore campaigns according to launch date by outcome, the relevant data was pulled into a pivot table that can be filtered by 'Parent Category' and displays the count of successful, failed, and canceled campaigns according to which month the campaign was launched.

!(https://github.com/semionaut/kickstarter-analysis/assets/images/outcome_by_month.png)

Separately, data for campaigns crowdfunding plays was selected and sorted according to goal amount and by outcome. Arguably, this is the most relevant data to Louise's project because it focused on crowdfunding campaigns for plays specifically and would allow her to evaluate the chance of success of her proposed >$10,000 funding request versus some other amount. It was straightforward to tally the number of campaigns according to different funding bands using the `COUNTIFS()` function with multiple conditions. As an example, the function used to find the total number of campaigns that were successful when seeking between $10,000 and $14,999:

```
=COUNTIFS(Kickstarter!$F:$F, "successful", Kickstarter!$D:$D, ">=10000",  Kickstarter!$D:$D, "<15000", Kickstarter!$R:$R, "plays")
```

Have a look at the resulting table below.

!(https://github.com/semionaut/kickstarter-analysis/assets/screenshots/outcome_by_amount.png)

##### Challenges & Potential Problems #####

When generating the table shown above, a series of manual changes were required to adjust the function between the different outcomes and funding bands. Rewriting the function manually is both time-consuming and potentially error-prone, not to mention impossible for a large dataset. The exercise of filling out column A as a string rather than a numerical value requires the low and high amount of each of the funding bands to be hardcoded into the function, preventing it from being filled down easily. Espressing the goal amount in two separate columns representing the low and high end of the band and allowing those to be expressed as a relative cell reference might have enabled filling the function down the column, only needing to be modified for the first and last rows of the table (which have 3 `COUNTIFS()` criteria instead of 4).

On the same table, when calculating the success rate (%) for successful, failed, and canceled campaigns, I was able to create a reproducible function that filled across the columns and down the rows by expressing column E as an absolute cell reference, i.e.:

```
=ROUND((B2/$E2),4)
```

!(https://github.com/semionaut/kickstarter-analysis/assets/screenshots/success_rate.png)

Another potential challenge I can envision with the Kickstarter data, which came to light when looking through the 'Edinburgh Research' is that the values for funding goal and amount pledged are distributed across 13 different currencies, which *should* necessitate standardizing these dollar amounts into common units (eg. $USD) before comparing, for example, funding goal and outcome, or average donation per backer. 

## Results, Conclusions & Future Directions ##

Through the process described above, the following charts were generated:

* Theater Outcomes by Launch Date
* Outcome by Goal Amount

##### Theater Outcomes by Launch Date #####

Looking at all theater campaigns in the dataset together, evidently May was where historically the most successful theater campaigns were funded. It might be inferred from the chart that funding for theater productions is at its highest in May, and continues dropping until later in the year, and therefore tempting to recommend that Louise launch her campaign starting in May. However May through July also typically see the highest total number of submitted theater campaigns, which might mean greater competition for those backers and those funds.

!(https://github.com/semionaut/kickstarter-analysis/resources/Theatre_Outcomes_vs_Launch.png)

Looking at this data another way, the overall success rate of May theater campaigns is only slightly higher than average:

!(https://github.com/semionaut/kickstarter-analysis/assets/screenshots/success_by_month.png)

##### Outcome by Goal Amount #####

The second table and chart explored the relationship between the funding goal and success rate for crowdfunding campaigns for plays. 

!(https://github.com/semionaut/kickstarter-analysis/resources/Outcomes_vs_Goals.png)

Evidently, campaigns asking less than $1,000 and $1,000 to $4,999 had the highest rate of success. The proportion of successful play campaigns decreases with higher target amounts. However, this data loses fidelity moving down the table as the success rate is often determined based on a very small number of total campaigns, probably too few to be reliable.

Louise mentioned her budget for the play would be around $10,000. Looking at the dataset another way, campaigns requesting a funding goal greater than $10,000 had a success rate of 45.57% whereas 69.97% of campaigns seeking a goal $10,000 were successfully funded. It might be the best advice for Louise to decrease the amount requested in order to improve her odds of her project being successfully funded.

##### Limitations #####

Trends, especially in the online marketplace, can change frequently and quickly. The Kickstarter data provided here stops in early 2017. Any conclusions made based on 2009 Kickstarter campaigns may not be very generalizable to today's outlook. This might ignore very relevant changes in the funding landscape, for example:

* has the average number of donors per campaign decreased over time, or stayed the same?
* do today's backers prefer to contribute to other genres of funding campaigns, such as film & video, or technology?
* Kickstarter used to be the most recognizeable crowdfunding platform, but alternative crowdfunding sites have proliferated and in some cases caught up. IndieGoGo, GoFundMe for example. Patreon gained prominence for creatives, content creators to fund artistic productions like films, shows, plays, or podcasts. Kickstarter might not be the only viable funding avenue for Louise to explore.

Moreover, there is no indication as to the completeness of this dataset. For instance, it contains a disproportionate number of theater campaigns which suggests these have been handpicked. In order to look at theater campaigns in the context of the broader, long-term trends in crowdfunding campaigns amount requested, pledged, average donation, number of backers, success rate, etc would require more confidence in the data being comprehensive (or a reasonable sampling).

##### Future Directions #####

As a next step, here are some new dimensions that should be explored in the data to better assist Louise with tailoring her campaign:

* Louise mentioned she was interested in the theater market in GB, while she is committed to creating a play in the US. It may be the case that theater campaigns have a wider following and higher potential for more backers in GB. It would be interesting to compare this between countries, and see if Louise should move her production elsewhere.
* There's some strategy involved in how widely to advertise the crowdfunding campaign. Often times, theater productions are supported by endowments and usually small numbers of very wealthy donors make up the bulk of the fundraising effort. It might be worht looking into which campaigns tend to have a higher rate of success, those with a large number of donors or a small number of high-value backers? (or no effect)
