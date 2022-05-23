The notebooks in this repo contain the code used for data acquisiton, EDA, local model construction, clustering, global model construction, and follow-up analysis for my dissertation. They are numbered in order of usage. Some notebooks are Python, some are R, as noted below. All notebooks were run on an AWS Sagemaker instance with 64GB RAM and 16 cores.

1. (Python) The first notebook contains the code needed to interact with the Portland-Vancouver traffic API to obtain data for that highway system. All raw data, whether gathered via this API or through a web interface, is stored in the Data/Raw directory.

2. (Python) Code to read in raw data and perform simple linear interpolation for missing values. Outputs from this notebook go to the Data/Intermediate directory.

3. (R) Code to explore missing data and perform imputation using modified temporal medians technique. Outputs from this notebook is written to the Data/Processed directory. These files are the ones we work with for the rest of the analysis/modeling. 

4. (R) Code to perform exploratory analysis.

5. (R) Code to perform baseline local modeling. These include ARIMA and naive forecasting models.

6. (Python) Code to perform FBProphet based modeling.

7. (R) Code to perform time series clustering.

8. (R) Code to perform pooled regression global modeling. 

9.1. (Python) Code to perform LightGBM global modeling with no hyperparameter tuning. 

9.2. (Python) Code to perform LightGBM global modeling with Bayesian hyperparameter tuning. 
 
9.3. (Python) Code to perform LightGBM local modeling with no hyperparameter tuning and with Bayesian optimization.

99. (R) Extra code for plots/follow up analyses