# Traffic in Ontario

Ensuring that people and goods are moved safely across Ontario is one of the major objectives of Ontario’s Ministry of Transportation (MTO). 

We examine a dataset, collected in 2006, containing information on traffic volumes of commercial and private vehicles at different survey stations in Ontario. 

The dataset has one week of average hourly truck and auto volumes data for every hour in a week for the year 2006. 

Our Quest,

* Examine the data set and infer insights about vehicular movement in the province. 
* Identify high Ontario traffic volume at certain peak hours of the day and in certain locations 
* Develop time-series plots of vehicle movement for the week 
* Establish relationships between time and traffic volume. 
* Identify hours of the day when commuters may escape traffic around some of the busiest areas in Ontario.

## Dataset Description

The data set is found at this [link](https://files.ontario.ca/opendata/2006_commercial_vehicle_survey_-_traffic_volumes_at_survey_stations.csv).

   | Field       | Field Description 
| ------------- |-------------|
| `Station ID`      |Unique CVS station number, representing the station from which the data was collected. It takes the form ONXXXX where XXXX is a 4-digit number. There are 104 unique station IDs present in the data set |
| `Station Name`     | Station name; each Station ID carries a station name      |
| `Direction` | This includes four different traffic directions; east, west, north and south.     |
| `MTO Region` | Five regions of MTO (Central, Eastern, Southwestern, Northern, Northwestern)      |
| `Highway or Road` | Highway number or road name. There are 33 unique highways in the data set      |
| `Location` | Description of location     |
| `Day of Week Number` | A number between 1 and 7 representing day of week. 1=Sunday, 7= Saturday      |
| `Hour` | Hour of day, 0 to 23 represents starting hour of the day (e.g. 12 represents 12 P.M. - 1 P.M.)      |
| `Single` | Number of single unit trucks      |
| `Multi` | Number of multi-unit trucks     |
| `Auto` | Number of cars and other passenger vehicles      |
| `Total Trucks` | Sum of single and multi-unit vehicles      |
| `Total Vehicles` | Number of total vehicles      |


## Data Analysis

Below is a description of the data

> The data does not seem to have any null values

```python
data.describe()
```
![image of data describe](./images/data.describe.png)

Below is a sample from the dataset,

```python
data.head()
```

![image of sample data](./images/data.sample.png)

The data under analysis is for a week timeframe in 2006 and has information on following data points,

* 33 highways
* 104 highway stations 
* 5 MTORegions

#### Data Skewness analysis (and logarithm scale to the rescue)

A simple facet grid shows the data skew,

```python
g = sns.FacetGrid(vehicles, col="variable", col_wrap= 2, sharey=False, sharex=False, height=10, aspect=2)
```
![image of data skew](./images/data.skew.png)

... a logarithmic transformation yields below,
```python
np.log(data1[['single','multi','auto','totalTrucks','totalVehicles']] + 1)
```

![image of data log](./images/data.logarithm.png)

Few insights we gain from this initial take on the data, 
* The distribution of totalVehicles parallels the distribution of auto suggesting that totalVehicles composition is made up more of auto than of any of the other vehicle types. 
* This could mean that there are more autos and passenger vehicles in the province than there are trucks. 
* The totalTrucks histogram shares more similarity with multi than it does with single. This could mean there are more multi trucks in the dataset, and possibly in Ontario, than there are single trucks.

#### Outlier identification
Box plots reveal the outliers in this dataset, however we are not ignoring them as they are valid data.
```python
for item in ['single', 'multi', 'auto', 'totalTrucks']:
    sns.boxplot(y=data[item], ax=ax[i,0])
    sns.boxplot(y=data[item][data[item]<=upper_fence(data[item])-5], ax=ax[i,1])
    i += 1
```
![image of data boxplot](./images/data.boxplot.png)

# Analysis

We proceed now to analyse the data based on each of the dimensions below,

* Stations
* Highways
* Directions
* Regions
* Day and Time of day

## Stations

### Station with large volume of data

```python
ax = stationsTraffic[:40]['trafficPercentage'].plot(kind='bar', figsize=(15,6))
plt.show()
```

![image of station traffic percent](./images/stations.percentTraffic.png)

> we can establish that auto contributes the most traffic of all the kinds of vehicles for this dataset.

### Station with high auto volume

```python
ax = stationsTraffic[['single', 
                      'multi', 
                      'auto']][:10].plot(kind='bar', figsize=(15,8))
plt.show()
```
![image of station auto volume](./images/stations.vehicleCountStation.png)

> Most stations show higher auto traffic than they do of other kinds of vehicles. ON0115 and ON0116 are some of the stations with the biggest auto traffic. Station ON0033 appears to have somewhat significant truck traffic. 

### Correlations ?

```python
xcorr_values.loc[i,j] = np.corrcoef(data['totalVehicles'][data['stationID'] == i],
             data['totalVehicles'][data['stationID'] == j]
            )[0,1]
```
![image of station correlation](./images/stations.correlation.png)

> Average correlations more than 70%, it appears traffic rises pretty much at the same rate all over the province and then settles back.
For ex. ON0026 and ON0027 correlates rather highly with traffic at ON0115

### How about time series analysis on traffic data ?

```python
data[data.stationID == 'ON0021'][25:50].reset_index()[['auto']].plot(figsize=(7,5))
```
![image of station timeseries](./images/stations.timeseries.png)

> ON0021 is located on Hwy 400 between King Rd & Kettleby Rd north of Vaughan. This spike looks to be the daily northerly homeward traffic from downtown Toronto 

>A driver who cares to avoid this traffic 
>* Can take alternative route as this traffic seems a daily occurence. 
>* He might as well leave the office just as the sharp traffic peak is settling after 1500hrs.

## Highways - ON0115

```python
data[data.stationID == 'ON0115'].highway[:4]
```

>  ON0115 located on Hwy 401 is the biggest daily traffic station.

### Hwy 401 Traffic distribution

```python
ax[i//2, i%2].plot(data[['single', 
                             'multi', 
                             'auto', 
                             'totalTrucks']][(data['highway'] == 'Hwy 401') & (data['stationID'] == j)], 
                       label=j)
    ax[i//2, i%2].set_title(j, fontsize=13)
```
![image of highway station traffic](./images/highways.stationtraffic.png)

The subplots showed that station ON0115 on Hwy 401 records some of the biggest traffic. From the cross correlation matrix examined earlier, we observed that some of the other stations correlate greatly with station ON0015. Ranking these values in descending order shows that ON0026 and ON0027 are some of the other stations with traffic which correlates rather highly with traffic at ON0115. This could mean they also register sizeable traffic. We could take a look.

```python
(data.groupby('stationID')
 .mean()
 .loc[['ON0115','ON0032','ON0033', 'ON0026']]
 .iloc[:, 2:]).sort_values(by='totalVehicles', ascending=False)
```
![image of highway station ON0115](./images/highways.stationON0115.png)

> * Traffic north of ON0026 appears relatively milder. This is probably because there is a toll and as such is avoided by most drivers. The traffic is therefore much less and seems a good alternative for the traffic-conscious driver. 
> * Station ON0115 on Hwy 401 remains heavy with traffic.

### Highway with heavy traffic

Let's sort traffic by highway and arrange the results in order of decreasing mean traffic count. 

The highways with the biggest traffic in Ontario appear to be 
*Hwy 401, 
* QEW, 
* King Edward Ave, and 
* Hwy 400. 

Hwy 401 runs the length of the south of Ontario extending from its end near Detroit in the United States to the east as far as Ottawa. It apparently is also very busy because it carries traffic westwards and eastwards into Ontario's major city, Toronto.

```
highwayTraffic = (pd.pivot_table(data=data, index='highway', values=['multi', 'auto', 'single', 
                                                    'totalTrucks', 'totalVehicles'], aggfunc='mean')
.sort_values(by='auto', ascending=False))
highwayTraffic['trafficPercentage'] = highwayTraffic['totalVehicles'] * 100 / highwayTraffic['totalVehicles'].sum()
highwayTraffic.sort_values(by='trafficPercentage', ascending=False)[:10]
```

![image of highway heavyTraffic](./images/highways.heavytraffic.png)
![image of highway heavyTrafficPlot](./images/highways.heavytrafficPlot.png)

>Hwy 401, QEW and King Edward Ave account for more than 50% of vehicular traffic in the province!

### Hwy 401, the well beaten track  

* Traffic appears to begin around 5am in the morning and increases gently during the day, peaking on major highways around noon. 
* Vehicle density does not seem to reduce until towards evening around 6pm, so that by midnight it goes down significantly. 

![image of highway heavyTraffic 401](./images/highways.401.monday.png)

>The consistency in these plots suggest that the probability of getting caught in traffic trends higher as the morning arrives. 
>To beat traffic, one would have to be on the road as early as 5-6am. Leaving any sooner or later does not appear to help.

![image of highway heavyTraffic 401](./images/highways.401.friday.png)

>On the last day of the week, it appears many more cars stay on the road even towards evening (parties? night outs?), as it appears vehicle density does not reduce significantly on this day unlike on others. 

### Highway vehicle count

Again, auto make up the highest vehicle count for every highway. Apparently, it would seem there are many more small cars in the Ontario province than there are trucks. 

![image of highway vehicle count](./images/highways.vehicle.count.png)

However, are there areas in the province where trucks may dominate?

We examine again the distribution of vehicular traffic for each highway and sort by totalVehicles and totalTrucks. This time we express totalTrucks as a fraction of totalVehicles to obtain truckPercentage for each highway.

```python
highwayTraffic = (data
                  .groupby(by='highway')
                  .mean()
                  .sort_values(by=['totalVehicles', 'totalTrucks'], 
                               ascending=False)[['single','multi','auto','totalTrucks','totalVehicles']])
highwayTraffic['truckPercentage'] = highwayTraffic['totalTrucks'] * 100 / highwayTraffic['totalVehicles']
highwayTraffic.sort_values(by='truckPercentage', ascending=False)[:10]
```

![image of highway vehicle count truck](./images/highways.vehicle.count.truck.png)

> Hwy 3 - Huron Church Rd, Hwy 402, and Hwy 405 have high truck traffic. 

>These highways with huge truck traffic are located close to border toll stations in Ontario. Suggests significant commercial activity at the borders of the United States and Canada.

## Directions

### Which direction has heavy traffic ?

```python
data.pivot_table(index="direction", columns=["dayWeekNo"], values=["totalVehicles"], aggfunc="mean")
```

> The highest traffic goes towards East. 

This is in the direction of downtown Toronto from areas west of GTA (as we saw in the maps shown earlier, the stations which registered this traffic are located west of Toronto). 

This is no surprise as the central business district is located in this direction. 

>Lesser traffic comes westbound into the province, and the least traffic comes from the northern and southern directions.

This assumes heavily that most traffic heads towards the city center.

## Regions

```python
ax = data.pivot_table(index="MTORegion", columns=["dayWeekNo"], 
                      values=["totalVehicles"], aggfunc="mean").T.plot(figsize=(10,7))
```

![image of Region MTO](./images/region.traffic.png)

>For every single day of the week, the central MTO region records the highest traffic. Central contributes nearly 70% of the traffic experienced each day in the province.

## Days and Hours

```python
round((((data.groupby('dayWeekNo').mean()).iloc[:, 1:4]).T * 100/ 
 (data.groupby('dayWeekNo').mean()).iloc[:, 1:4].sum(axis=1)).T, 2).plot(kind='bar', figsize=(16,7))
```

![image of Days and Hours](./images/daysNhours.trafficpercentage.png)


> In this case the raw traffic count offers no more insight than the percentage count.

### Hours with high traffic volume

How does 11 in the morning and 4 in the evening look like ?

```python
round((((data.groupby('hour').mean()).iloc[:, 1:4]).T * 100/ 
 (data.groupby('hour').mean()).iloc[:, 1:4].sum(axis=1)).T, 2).plot(kind='bar', figsize=(16,7))
 ```

![image of Days and Hours 11AM](./images/daysNhours.11AM4PM.png)

## Machine Learning - Clustering

We attempt to scale and cluster the data set in order to gain some additional insights.

```python
kmeans = KMeans(n_clusters=10)
kmeans.fit(X)
y_kmeans = kmeans.predict(X)
```

Scatter plots 

```python
    ax[i//2,i%2].scatter(X[attrb], X['totalTrucks'])

    ax[i//2,i%2].set(xlabel=attrb,
                     ylabel='totalTrucks', 
                     title=attrb+' vs. totalTrucks')
    x = np.linspace(0,max(X[attrb]),50)
    y = np.linspace(0,1600,50)
    ax[i//2,i%2].plot(x,y, linewidth=3, linestyle="-.", color='red')
```

![image of ML scatter plot](./images/ML.scatterplot.png)

>The scatter plot appears to suggest that when there are lesser numbers of totalTrucks on the road, they are mostly multi trucks. 

>The scatter plot multi vs. totalTrucks has nearly a slope of 1 at lower values. This suggests that the number of totalTrucks is almost as much as the number of multi. As a result, one can infer that there are more multi unit trucks on Ontario roads than there are single unit trucks.

>The auto vs. totalTrucks scatter plot offers better insight. Clearly, there are many more auto than there are other vehicles. At areas of high auto density, it appears traffic of totalTrucks is low. This is because there are more scatter points on the south side of the plot than there are on the north end. 


Here is color coded scatter plot based on stationID

```python
plt.scatter(X_scaled[:,2], X_scaled[:,3], c=x1, cmap='viridis')
```

![image of ML scatter plot color](./images/ML.scatterplot.color.png)


>Areas of high traffic also seem to have totalTrucks and auto in near equal measure. This could suggest that passenger vehicles and trucks contribute near equal traffic in the province.



kmeans does appear to have done an excellent job in correctly clustering the data set into regions from very low traffic to rather high traffic. Lower traffic regions are closer to the origin, while higher traffic regions are away from the origin.

```python
plt.scatter(X_scaled[:,2], X_scaled[:,3], c=y_kmeans, cmap='viridis')
```

![image of ML scatter plot color labels](./images/ML.scatterplot.color.label.png)


## Forecasting

Next, we attempt to forecast traffic on the busiest highway, Hwy 401, using the time series data available. We use a simple fft algorithm for this task.

```python
data[['single', 'multi', 'auto', 'totalTrucks']][data['stationID']=='ON0115'].plot(figsize=(10,8))
```
![image of ML forecasting](./images/forecasting.initial.png)




## Traffic Probability

## Conclusion

Based on our examination of the data below are the insights we could derive,


* Some of the biggest traffic in the province happen on Hwy 401, QEW and King Edward Ave. 

>While these may have the highest density of cars and other passenger vehicles, they do not feature among the list of highways which carry truck traffic. These highways, which have the biggest truck traffic, are much closer to the border of the province than they are to the city center. We also found that passenger vehicle traffic consititute the biggest traffic in the province. More passengers move in the province than do goods. While most traffic happen towards the province's financial district of Toronto, other areas outside of Toronto also have traffic which is just as significant, for example QEW around Hamilton. However, the highest traffic is towards the east. This could also be because of the location of the city's financial district with respect to all other locations. The northern and southern directions carry the least traffic.

* Hwy 401 traffic begins to increase at around 5am and peaks at noon, it appears that for the first and last day of the week, the slopes are much gentler than during the other days of the week.

>Could this suggest some lethargy preventing people from hurrying to work on a Monday and more people relaxing at home on the weekends compared to other busy days of the week? 

* The other denser highway traffic on QEW and King Edward Ave also follow this trend. 

>Traffic on the last day of the week also did not peter out in the evening as does traffic on other days, possibly suggesting that more people spent longer nights out during the last day of the week?

Clustering offers an avenue to cluster the data set into a spectrum ranging from high traffic to minimal traffic based on the size of the traffic for any given hour in a day. This generates an output that could help with labeling the data set and with which further classification could be done if provided with new data set. 

* We went on to predict some future traffic on the biggest highway, Hwy 401 using fft. 

>The easy predicatability of traffic patterns makes fft a suitable method for this job, as it can easily duplicate repetitive changes characteristic of traffic.

* Finally, we used logistic regression to examine the chances of getting stuck on a particular highway. 

>With the generated model we were able to reinforce our earlier conclusion that Hwy 401 has the biggest vehicle movement and offers the greatest chance of being stuck in traffic. The probability was as high as 80%. We saw also that some other highways, like Hwy 138, offer the lowest possibility of traffic. Sample gifs obtained from google maps helped verify our analysis. Comparing our gifs, obtained in 2020, to results from a data set from 2008 shows some eerie similarity which suggests that traffic patterns may not have changed that greatly in the province over the past years.



## Contributors
Contributors to this group project include [manchuran](https://github.com/manchuran), [harrisrahman](https://github.com/harrisrahman), and [Moh-M](https://github.com/Moh-M).

