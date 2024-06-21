#### Place for Dataset Stored and Preprocess Conducted

# Data Preprocessing for Stunting Analysis
This repository contains code to preprocess a dataset related to stunting analysis. The preprocessing steps include reading the dataset, cleaning and transforming the data, and saving the cleaned data to a CSV file.
We got the data from actual 'Posyandu' of one of our member's hometown, to ensure that the data we have is credible and reliable. 
The original dataset consist of many column, but we're not using it all. So, the first step after we got the data is doing the preprocessing data. 
 
## PreProcess Data
### 1. Read and Clean the Data
- Read the CSV file using Pandas
- Drop unnecessary columns

### 2. Fix Invalid Values
- Define functions to convert weight, height, and age
- Apply the conversion functions to relevant columns

### 3. Handle Missing Data
- Check for columns with null values
- Drop rows with null values

### 4. Rename Columns and Adjust Values
- Rename columns for simplicity

	    data.rename(columns={"JK": "gender", "Usia Saat Ukur": "age", "Berat": "weight", "Tinggi": "height", "ZS BB/U": "zs_weight_age", "ZS TB/U": 		"zs_height_age", "ZS BB/TB": "zs_weight_height"}, inplace=True)

- Replace gender labels and strip whitespace:
	
        data['gender'] = data['gender'].replace({'L': 'male', 'P': 'female'}, regex=True)
	    data['gender'] = data['gender'].apply(lambda x: x.strip())

- Round age to the nearest integer and other columns to 2 decimal places

        data['age'] = data['age'].astype(int)
        columns_to_convert = ['weight', 'height', 'zs_weight_age', 'zs_height_age', 'zs_weight_height']
        for col in columns_to_convert:
            converted_values = data[col].apply(convert_weight_height)
            data[col] = converted_values.round(2) if converted_values.dtype != 'O' else converted_values
     
### 5. Calculate and Transform Z-Score Columns
- Create a new column by averaging three z-scores
- Transform the average z-score to a percentage
- Round the percentage column to 2 decimal places

### 6. Save Cleaned Data



#
## Parameters we used
    X = data[['gender (string: male, female)',
        'age(int: 0-60 bulan)',
        'weight(float: kg)',
        'height(float: cm)']]
  
## Target output
    y = data[['zs_weight_age',
        'zs_height_age',
        'zs_weight_height',
        'totalzs_3',
        'totalzs_percentage']] #all float
  
### Scaling of Stunted State
    zs_weight_age, zs_height_age, zs_weight_height: Z-score gizi berdasarkan Age, Gender, Weight, Height 
    (-3 < zscore < +3)

### Calculating the Total Z-Score
    totalzs_3: The average number of 3 Zscore 
    (-3 < mean total zscore(mtz) < +3) (Kurang Gizi = mtz < -1 (misal=-1.1) , Obesitas = mtz > +1 (misal=+1.3), Gizi normal = -1 < mtz < +1 (misal: +0.6)

### totalzs_percentage
    result of value transform mtz to percentage(%)
    (100% < zscore < 100)

## def GenderEncode
def genderEncode(gender, age, weight, height):

    # Label encoding gender
    gender_encoding = {'male': 1, 'female': 0}
    # Encode gender
    gender_encoded = gender_encoding.get(gender)

    return gender_encoded, age, weight, height

    
# def normalize_data with MinMaxScaler()
	def normalize_data(gender_encoded, age, weight, height):
	    normalization_model = "normalization_model.joblib"
	    # Load the saved scaler using joblib
	    scaler = joblib.load(normalization_model)
	    feature = np.array([[gender_encoded, age, weight, height]])
	    # Normalize the data using Joblib
	    normalized_data = scaler.transform(feature)

	return normalized_data
