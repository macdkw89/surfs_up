# surfs_up

Python, Pandas, sqalchemy

# Overview

The purpose of this analysis is to determine whether or not W. Avy's proposed ice cream business in Hawaii is sustainable year round. We will conduct this analysis by analyzing data from the months of June and December. 

# Results

The tables containing the data can be seen below:

![tables](/Resources/describe_tables.png)

Key Takeways: 
- The mean temperatures for June and December are roughly only 4 degrees different, which means the weather relatively consistent year round.
- The max temperature for December is only 2 degrees less than June, which means there are still plenty of 'hot' days in December where people will be more likely to buy ice cream.
- The primary difference between June and December weather is that December fluctuates more towards the lower end of the temperature spectrum, but still has a similar "max" temperature. 

# Summary

**Preliminary verdict: Yes**, the proposed ice cream business should be sustainable year round. *However*, there is still more analysis to be done.

The hawaii.sqlite database gives us more information than just the temperature; it also gives us precipitation amounts. I ran the same method we used for temperature, but for precipitation and found the following:

![precipitation](/Resources/precipitation_tables.png)

This shows us that June has a mean precipitation rate of 0.136 and December has 0.217, which is a 59% increase. That means December is much rainer than June, and that will affect business at the ice cream shop. 

If we go deeper into the data, we can seperate the precipitation data by station ID and find out if certain areas are more or less prone to rain. The following table reflects that data:

![stations](/Resources/precipitation_by_station.png)

What we find is that there are some stations where rain is MUCH more prevalent. W. Avy can use this information alongside other information, such as tourism traffic, real-estate costs, etc. in order to find the best location possible for his ice cream business

## Code Blocks used for additional analysis
These are the code snippets I used to find the information used in the Summary section:

```python
# Average Precipitation for December and June
precipitation_in_june = session.query(Measurement.date, Measurement.prcp).filter(extract('month', Measurement.date) == 6).all()
precipitation_in_june_list = [i.prcp for i in precipitation_in_june]
precipitation_in_june_df = pd.DataFrame(precipitation_in_june_list, columns=['June Precipitation'])
precipitation_in_december = session.query(Measurement.date, Measurement.prcp).filter(extract('month', Measurement.date) == 12).all()
precipitation_in_december_list = [i.prcp for i in precipitation_in_december]
precipitation_in_december_df = pd.DataFrame(precipitation_in_december_list, columns=['December Precipitation'])
print(precipitation_in_december_df.describe())
print(precipitation_in_june_df.describe())
```

```python
# Average Precipitation for each month by Station
station_names = session.query(Measurement.station).distinct().all()
station_precipitation_june = session.query(Measurement.station, func.avg(Measurement.prcp)).filter(extract('month', Measurement.date) == 6).group_by(Measurement.station).all()
station_precipitation_june_df = pd.DataFrame(station_precipitation_june, columns=['Station', 'June Precipitation'])
station_precipitation_december = session.query(Measurement.station, func.avg(Measurement.prcp)).filter(extract('month', Measurement.date) == 12).group_by(Measurement.station).all()
station_precipitation_december_df = pd.DataFrame(station_precipitation_december, columns=['Station', 'December Precipitation'])
print(station_precipitation_june_df.sort_values(by=['June Precipitation'], ascending=False))
print(station_precipitation_december_df.sort_values(by=['December Precipitation'], ascending=False))
```
