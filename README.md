# NYC_taxi_and_ACS_data

This project aimed to **explore a large dataset such the NYC taxis public data** (Jan, Apr and July months, 2015) **and the American Community Survey (ACS demographic and socio-economic data (by block group)** to **get insight into the transportation sector in New York.**

The **main objetives** where:

- Extract, process and explore the data and **get insights** from it.
- **Retrieve the average number of pickups by block group.**
- **Train a model** based on the previous objetive.

The **following information is a resume of the [jupyter notebook](https://github.com/Sampayob/-NYC_taxi_and_ACS_data/blob/main/NYCtaxi_ACS_dataExploration_and_modelling.ipynb)** which contains all the process, code and plots an the same explanations.

_***Note: the jupyter notebook may need to be downloaded** due to I had previsualization errors._

## Data

- The **NYC taxis data contains 37383561 entries**, being a large and heavy dataset (**6GB aprox**). It **was obtained from a google storage unit** to, as a first aproach, avoiding the download and accessing directly to it, although **the dataset was finally converted into a SQLite database for faster and memory saving  manipulation**. It contained **information about pickups and dropoffs, fares, date-time, etc.**

- The **ACS** data contains entries with information about **geoid/block groups, demographical variables with its values.**

- A **GeoJSON** was also used with information about the **same geoid/block groups of the ACS data and polygons of the bock groups.**

## ETL

- **Data extraction**: as commented before, the data was obtained from a google storage unit. The NYC taxis data was divided into **77 files aprox  which were joined in a .csv file and imported into a SQLite database for easy manipulation through querys after experiencing RAM memory errors with the .csv file.**

- **Data transformation**: 
  - **NYC taxis data**: although variable types where defined when creating the SQLite database, **they needed in some cases a data type transformation after retrieving a query.**
  - **ACS and GeoJSON data**: only needed to **join both dataframes by geoid/block group into a unique dataframe and create from it a geodataframe** to been able to manipulate the geometric data for ploting and also to obtain the intersection with the NYC taxis pickups.

## Data exploration

- **NYC taxis data**:
  ![alt text](https://github.com/Sampayob/-NYC_taxi_and_ACS_data/blob/main/plots/pickups_dropoffs_locations.png)
  - The pickups and dropoffs latitude and longitude data was plotted to geographically visualize it. The NYC shape obtained confirmed the geodata seems good.
  ![alt text](https://github.com/Sampayob/-NYC_taxi_and_ACS_data/blob/main/plots/pickups_dropoffs_hours.png)
  ![alt text](https://github.com/Sampayob/-NYC_taxi_and_ACS_data/blob/main/plots/pickups_dropoffs_months.png)
  
  - The pickup and dropoffs hours pointed that most of the pick ups are between 6-8 PM and off-pikcups hours around 5 PM and in July there are less trips.
  ![alt text](https://github.com/Sampayob/-NYC_taxi_and_ACS_data/blob/main/plots/payment_type.png)
  - Credit cash payment is overall prefered (1).
  ![alt text](https://github.com/Sampayob/-NYC_taxi_and_ACS_data/blob/main/plots/Ratecodeid.png)
  - Almost every trip have a standard rate, with a minority with a JFK airport rate or negociated rates (1).
  ![alt text](https://github.com/Sampayob/-NYC_taxi_and_ACS_data/blob/main/plots/fare_amount.png)
  - The fare amounts (per meter) are grouped in a <2000$ range although it seems to high.
  ![alt text](https://github.com/Sampayob/-NYC_taxi_and_ACS_data/blob/main/plots/trip_distance.png)
  - Most of the trips are short or mid-distance with a few really long ones.
  ![alt text](https://github.com/Sampayob/-NYC_taxi_and_ACS_data/blob/main/plots/passenger_count.png)
  - The duration of a trip is similar with 1, 2, 3 or 4 passengers. This duration increases logically when the cab is previously booked in a certain location.
  
- **ACS and GeoJSON data**:
  ![alt text](https://github.com/Sampayob/-NYC_taxi_and_ACS_data/blob/main/plots/blockgroups_commute.png)
  - The mayority of commuters come from the 3600X, 3605X, 3606X and 3608X geoids/block groups.
  ![alt text](https://github.com/Sampayob/-NYC_taxi_and_ACS_data/blob/main/plots/nyc_geo.png)
  - The NYC block groups are represented in the GeoJSON data and the boroughs can be infered by geoid (Bronx, Brooklyn, Manhattan, Queens, Staten Island).
  
## Average number of pickups by block group

_***Note**: **due to constant RAM memory errors when performing a spatial join (because the large NYC taxis dataset), a little sample of the NYC taxi dataset (5000 entries per month) was made to continue the process from this point.** Although the results are not crearly representative of all the entire dataset, **this aproach allows to at least continue the data analysis process and modelling.**_

After perfom a spatial join to merge the NYC taxi pickups coordiantes and the ACS block groups polygons to optain the number of pickups contained in each block group. The average number of pickups by block group was **number**.

## Modelling
Previous to the modelling there was some data analysis:
![alt text](https://github.com/Sampayob/-NYC_taxi_and_ACS_data/blob/main/plots/avg_pickups_dist.png)
The target label is skewed and have high curtosis (have a long tail and is peaked)
It was transform by logarithm to have a more normal-like distribution which helps the algorithm:
![alt text](https://github.com/Sampayob/-NYC_taxi_and_ACS_data/blob/main/plots/avg_pickups_dist_log.png)
- The **Gradient Boosting algorithm** was chosen to perform the **estimation of the average number of pickups per block group. Hyperparameter tunning with GridSearchCV and K-Fold cross validation were applied** to find the best hyperparameters and also validate the model.
- The **RMSE was 0.000888** which is a little high. Due to this model has not been trained with all the data it is clear the model can be improved feeding it with more data and also testing other algorithms and with hyperparameter tuning.

## Conclusions
- Work with large files is complicated and time-costly if you have an average computer (and some times impossible if you ran out of RAM memory). It is clear cloud computing is needed to agilize and empower all big data process.All process on the cloud like accessing data, transformations and model training tend to be more efficient.
- Load data in chunks is essential sometimes to perform efficient manipullation.
- The ETL process could be scaled for larger data with pipelines, pretrained objects (scalers, encoders...), etc., automatizing the extraction, transformation, data cleaning, etc. Also the modelling could be done with distributed tools to accelerate it (Spark, Amazon Sagemaker...)
- Future improvements for the model are: more accurate feature transformations, test more estimators to find which one suits best, test more hyperparameters, try K-Fold and Leave-one-out cross validation...
- In modelling it is important to choose the correct algorithm but also to get the best of it with hyperparameter tunning and adecuating the variables to enpower it by doing a correct data analysis and feature engineering.
