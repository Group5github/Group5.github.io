# Traffic in Ontario

Ensuring that people and goods are moved safely across Ontario is one of the major objectives of Ontario’s Ministry of Transportation (MTO). To this end, the ministry often conducts surveys of goods, goods value, passengers and vehicular movement across its five regions around the province. Survey points are mounted at different stations across the province where data is collected, often of the volume of traffic or goods. In this dataset, obtained in 2006, information is provided about solely of the traffic volumes of different commercial and private vehicles at different survey stations in Ontario. It has average hourly truck and auto volumes for only one week in the stated year. According to the metadata provided, the data is based on traffic counts collected over a two-week period during the routine Commercial Vehicle Survey (CVS) conducted by the Ontario Ministry of Transportation. We examined this data set and obtained a few key insights about vehicular movement in the province. We found that the volume of traffic is especially high at certain peak hours of the day in certain locations around Ontario, developed a time-series plot of vehicle movement over the week and established some relationships between time and traffic volume. We also revealed at what hours commuters may be able to escape traffic around some of the busiest areas in the province.

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

A simple facet grid show the data skew,

```python
g = sns.FacetGrid(vehicles, col="variable", col_wrap= 2, sharey=False, sharex=False, height=10, aspect=2)
```
![image of data skew](./images/data.skew.png)

... a logarithmic transformation yields below,
```python
np.log(data1[['single','multi','auto','totalTrucks','totalVehicles']] + 1)
```

![image of data skew](./images/data.logarithm.png)

Few insights we gain from this initial take on the data, 
* The distribution of totalVehicles parallels the distribution of auto suggesting that totalVehicles composition is made up more of auto than of any of the other vehicle types. 
* This could mean that there are more autos and passenger vehicles in the province than there are trucks. 
* The totalTrucks histogram shares more similarity with multi than it does with single. This could mean there are more multi trucks in the dataset, and possibly in Ontario, than there are single trucks.

## Research Questions

Some of the research questions we examined include: 

1. Which station records the highest volume of traffic? What proportion of vehicles makes up the highest traffic volume? 

2. Which highway experiences the highest traffic volume? What proportion of vehicles makes up the highest traffic volume? 

3. What does the daily traffic distribution for each station look like? 

4. On what day of the week is the highest traffic volume registered? 

5. What are the chances of getting stuck in traffic in Toronto downtown when returning from work at 1600hrs in the afternoon? 

6. What is the best time to leave home for work, if working in downtown Toronto? Are there possible favorable alternative routes? 

7. Can we predict the state or nature of traffic on certain highways in the province? 

8. Can we possibly apply clustering to this dataset to see the highway most affected by traffic? 


## Contributors
Contributors to this group project include [manchuran](https://github.com/manchuran), [harrisrahman](https://github.com/harrisrahman), and [Moh-M](https://github.com/Moh-M).

