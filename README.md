# Traffic in Ontario

Ensuring that people and goods are moved safely across Ontario is one of the major objectives of Ontario’s Ministry of Transportation (MTO). To this end, the ministry often conducts surveys of goods, goods value, passengers and vehicular movement across its five regions around the province. Survey points are mounted at different stations across the province where data is collected, often of the volume of traffic or goods. In this dataset, obtained in 2006, information is provided about solely of the traffic volumes of different commercial and private vehicles at different survey stations in Ontario. It has average hourly truck and auto volumes for only one week in the stated year. According to the metadata provided, the data is based on traffic counts collected over a two-week period during the routine Commercial Vehicle Survey (CVS) conducted by the Ontario Ministry of Transportation. We examined this data set and obtained a few key insights about vehicular movement in the province. We found that the volume of traffic is especially high at certain peak hours of the day in certain locations around Ontario, developed a time-series plot of vehicle movement over the week and established some relationships between time and traffic volume. We also revealed at what hours commuters may be able to escape traffic around some of the busiest areas in the province.

## Dataset

The data set is found at this [link](https://files.ontario.ca/opendata/2006_commercial_vehicle_survey_-_traffic_volumes_at_survey_stations.csv). 

## Dataset Description

It contains 17,472 records and 13 attributes.  The attributes, as given in the provided data set, include `Station ID`, `Station Name`, `Direction`, `MTO Region`, `Highway or Road`, `Location`, `Day of Week Number`, `Hour`, `Single`, `Multi`, `Auto`, `Total Trucks`, `Total Vehicles`. For easy handling, all of these attributes were renamed in the `.ipynb` file to be used for analysis.

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
Contributors to this group project include [manchuran](https://github.com/manchuran),

