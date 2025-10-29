# NYC Squirrel Census ETL Pipeline

## Assessment note
The main code for this project can be found in [transform_data.ipynb](https://github.com/Lars-Codes/NYPL/blob/main/transform_data.ipynb) in the root directory. 

The CSV data dumps can be found in the directory [data_dump](https://github.com/Lars-Codes/NYPL/tree/main/data_dump). 


## Introduction
This project implements a simple ETL pipeline using data from the [2018 Central Park Squirrel Census](https://data.cityofnewyork.us/Environment/2018-Central-Park-Squirrel-Census-Squirrel-Data/vfnx-vebw).  

Please note that the purpose of integrating the tree census data was to roughly estimate the tree the squirrel was in if it was "above ground." Of course, the squirrel may not necessarily be in a tree if it was above ground, but this may be useful data in for identifying commonalities between trees squirrels use for their homes. 

That being said, the tree data used was primarily for trees on the streets of New York City, not inside of Central Park. Therefore, each squirrel is recorded as being very far from most trees in the tree dataset. However, I wanted to set up this pipeline as an example for when this data may be available in the future. 

---

## Data sources 
- **Primary:** [2018 Central Park Squirrel Census](https://data.cityofnewyork.us/Environment/2018-Central-Park-Squirrel-Census-Squirrel-Data/vfnx-vebw)
- **Secondary:** [2015 Central Park Squirrel Census](https://data.cityofnewyork.us/Environment/2015-Street-Tree-Census-Tree-Data/uvpi-gqnh/about_data)

Both datasets were downloaded from the [NYC Open Data portal](https://opendata.cityofnewyork.us/).

---

## Transformations

| Transformation | Description | Purpse | 
|----------------|-------------|-------------|
| **Calculate number of squirrels per hectare** | Counts number squirrels per hectare. | This data will reveal where the highest and lowest concentration of squirrels are in Central Park. | 
| **Calculate eating elevation** | For each recorded elevation of the squirrels, count how many squirrels are eating at that elevation. | This data tells us if squirrels are more likely to be eating above ground (like in a tree) or on the ground, and if they are above ground, how high up they are. | 
| **Date Parsing** | Converts the date field into a human-readable datetime and extracts `year`, `month`, `day`, and `weekday`. | This may make it easier for future users to select data from a certain timerange. |  
| **Activity Score** | Of the recorded activities, i.e. `running`, `chasing`, `climbing`, `eating`, and `foraging`, the activity score counts how many of those activities the squirrels are doing at once. | This data can tell us about simultaneous squirrel activity.|
| **Distance Calculation** | Computes the distance in meters between each squirrel and its nearest tree using geographic coordinates. | This data pipeline can help the user identify the specific tree the squirrel was found. |

---

## Exported data

**Generated Files**
| File Name | Description |
|------------|-------------|
| `squirrel_census_transformed.csv` | Full dataset including parsed dates, activity scores, and nearest tree distances. |
| `squirrels_per_hectare.csv` | Aggregated squirrel counts by hectare. |
| `num_squirrels_eating_per_elevation_aboveground.csv` | Counts the number of squirrels eating per elevation. If the value for elevation is NONE, the eating squirrel was observed on the ground.|

Each CSV file is encoded in UTF-8 and uses comma delimiters.

---

## Output Data Format

The original data in the squirrel census dataset can be found [here](https://data.cityofnewyork.us/Environment/2018-Central-Park-Squirrel-Census-Squirrel-Data/vfnx-vebw/about_data). The table below represents additional columns added post data transformation, but does not reflect every data column represented in this CSV file. 

### **squirrel_census_transformed.csv**
| Column | Description |
|---------|-------------|
| `year`, `month`, `day`, `weekday` | Extracted date components |
| `activity_score` | Sum of behavioral indicators (0–5) |
| `nearest_tree` | Tree ID of the closest tree from the Tree Census |
| `distance_meters` | Distance to nearest recorded tree (in meters) |

### **num_squirrels_eating_per_elevation_aboveground.csv**
| Column | Description |
|---------|-------------|
| `above_ground_sighter` | Elevation of squirrel aboveground if eating. False if the squirrel is eating on the ground.|
| `eating_squirrel_count` | Number of squirrels at the given elevation. |

### **squirrels_per_hectare.csv**
| Column | Description |
|---------|-------------|
| `hectare` | Hectare ID |
| `squirrel_count` | Number of squirrels at that hectare. |


---

## Dependencies
- Python version used for development: 3.13.9

- pandas  
- sodapy  
- numpy  
- scipy  
- geopandas

Install dependencies:
```bash
pip install pandas geopandas numpy scipy sodapy
```

You may also install dependencies with the requirements.txt file located in the root directory: 
```
pip install requirements.txt
```

## How to run 

1. Install dependencies (see above)

2. Create an account on [NYC Open Data portal](https://opendata.cityofnewyork.us/) and generate an APP Token 

3. Create a .env file with your information: 
```
APP_TOKEN="JkBpJ9IDVBgTg6AxU4diKwoqT"
USERNAME="your_email@email.com"
PASSWORD="your_password"
DATA_DUMP_DIR="./directory_to_put_csv_dummp"
```

4. See transform_data.ipynb and choose which data transformation you'd like to run