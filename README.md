# Repo Overview
The notebooks in this repo contain the code used for data acquisiton, EDA, local model construction, clustering, global model construction, and follow-up analysis for my dissertation. They are numbered in order of usage. Some notebooks are Python, some are R, as noted below. All notebooks were run on an AWS Sagemaker instance with 64GB RAM and 16 cores, as well as an AWS EC2 instance of equivalent size. Both Sagemaker and EC2 utilized the Amazon Linux 2 operating system. Use of a different operating system (e.g., Ubuntu) was tested briefly, with slightly varied numerical results. Please also note that the Bayesian Optimization library used in notebooks 9.2 and 9.3 may not perform identically on different computers, even with a random seed set as is done in this work. On my Sagemaker and EC2 instances, both of which were running the Amazon Linux 2 operating system, some numerical differences were observed. This is likely becuase the machines have different processers. The Sagemaker instance was running Intel(R) Xeon(R) Platinum 8175M CPU @ 2.50GHz processors while the EC2 instance was using Intel(R) Xeon(R) Platinum 8259CL CPU @ 2.50GHz processors. See fmfn/BayesianOptimization#155 for more detail.

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

10. (Python) Code to construct and test DeepAR models using the GluonTS implementation.

11. (Python) Code for initial processing of data from unseen sensors.

12. (R) Code to impute the data from the unseen sensors.

13. (R) Code to assign the data from the unseen sensors to the appropriate clusters for each clustering technique used.

14. (R) Code to forecast data from the unseen sensors using Pooled Regression.

15. (Python) Code to forecast data from the unseen sensors for the default LightGBM models.

16. (Python) Code to forecast data from the unseen sensors for the LightGBM models trained with Bayesian Optimization.

17. (Python) Code to forecasts data from the unseen sensors using DeepAR.

18. (R) Local baseline and ARIMA models for the unseen sensor data

19. (Python) Local LightGBM models for the unseen sensor data

99\. (R) Extra code for plots/follow up analyses


# Installation and Usage Guide
If you are using EC2, the following instructions should enable you to set up the necessary conda environments and run the code from this repo. If you are using Linux (but not EC2), these instructions should generally apply as well. Use of conda is not required for running the code from this repo, but following these instructions ensures that your environment is similar to what my environment was like when I developed this code. If you have a prefered way of managing python packages or running R-based jupyter notebooks, that should work as well. 

## Installing Conda
On a fresh (AL2) EC2 instance, download the Miniconda installer shell script with the following command:
```wget https://repo.anaconda.com/miniconda/Miniconda3-py38_4.12.0-Linux-x86_64.sh```

Use the following command to run the installer:
```sh Miniconda3-py38_4.12.0-Linux-x86_64.sh```

* Type yes whenever prompted

Next, update the path so that you can use Miniconda
```export path export PATH="/home/ec2-user/miniconda3/bin:$PATH"```

* Run ```conda list``` to check that conda is working

### Creating a Conda Env for Python    
We will now create a conda environment for running any Python notebooks (R notebooks should be run in a separate conda environment because conda and R do not play as nicely together as Python and conda, see below for instructions)

Run the following command to create a conda env with Python version 3.8.12
* ```conda create -n thesis_env python=3.8.12 ipython notebook```

Run the following command to activate the environment
* ```conda activate thesis_env```
* If this does not work, you may need to restart the machine
    
You can now conda/pip install the libraries required for these notebooks

I ran these commands one-by-one to install all necessary libraries (I'm sure there is a faster way):
* ```conda install numpy=1.20.3```
* ```conda install pandas=1.3.4```
* ```conda install matplotlib=3.5.0```
* ```conda install scikit-learn=1.0.1```
* ```conda install joblib=1.1.0```
* ```conda install tqdm=4.62.3```
* ```conda install -c conda-forge scipy=1.5.3```
* ```conda install requests=2.26.0```
* ```conda install pandasql=0.7.3```
* ```conda install openpyxl=3.0.9```
* ```conda install -c conda-forge prophet=1.0.1```
* ```conda install -c conda-forge lightgbm=3.3.2```
* ```conda install -c conda-forge swifter=1.1.3```
* ```conda install -c conda-forge bayesian-optimization=1.1.0```
* ```pip install mxnet==1.9.1```
* ```pip install gluonts==0.9.4```
 
Deactive this conda environment by running:
```conda deactivate```

### Creating a Conda Env for R
Create a second conda environment for R-based notebooks by running:
```conda create -n thesis_env_r``` 

Activate the environment:
```conda activate thesis_env_r```

Install R and the R-essentials package from conda. I use R version 4.1.3:
* ```conda install -c conda-forge r-base=4.1.3```
* ```conda install -c conda-forge r-essentials```

Now run R (just type `R` in your terminal)

Install the required packages from CRAN, using the `install.packages` function and your preferred mirror (I used USA: OH, number 76)
    ```install.packages(c("tidyverse","timetk","ggridges","cluster","forecast","Metrics","Rcatch22","tseries","factoextra","tsfeatures","dtwclust","biglm","scoringutils"))```

The R package versions used in this repo are as follows:
* tidyverse = 1.3.1
* timetk = 2.8.1
* ggridges = 0.5.3
* cluster = 2.1.3
* forecast = 8.16
* Metrics = 0.1.4
* Rcatch22 = 0.2.1
* tseries = 0.10-51
* factoextra = 1.0.7
* tsfeatures = 1.0.2
* dtwclust = 5.5.10
* biglm = 0.9-2.1
* scoringutils = 1.0.0

You may run into some issues with these installs which require debugging. In my case, `factoextra` did not install the first time around. This was because cmake was not installed. I was able to install cmake by exiting R (running `quit()`) and running `conda install -c anaconda cmake`

I then started R again (by typing `R`) and re-ran `install.packages("factoextra")`, which worked this time.

Next, exit R (`quit()`) and deactivate the conda environment by running `conda deactivate` 

Note: if jupyter does not work in this env by default, you may also need to install ipython and notebook with simple conda install commands
* `conda install ipython`
* `conda install notebook`

### Cloning the Github Repo
You are now ready to clone the github repo. I am assuming you have git installed. If you do not, this can typically be installed by running `sudo yum install git -y` or by following instructions such as https://cloudaffaire.com/how-to-install-git-in-aws-ec2-instance/ or https://git-scm.com/book/en/v2/Getting-Started-Installing-Git. Once git is installed, clone the git repo from https://github.com/lojzimjm/dissertation.
For example, run ```git clone https://github.com/lojzimjm/dissertation.git```

To open the notebooks, activate the relevant conda environment (e.g., `conda activate thesis_env`) and follow these instructions to connect to jupyter on ec2: https://towardsdatascience.com/setting-up-and-using-jupyter-notebooks-on-aws-61a9648db6c5

### Running the Code

#### Some words of caution about using the repo in general
* The code in this repo is meant to be run in order. While notebooks can be run out of order, there may be dependcies on earlier notebooks which cause problems. For example, all notebooks which perform global modeling depend on the clustering results of notebook 7, so notebook 7 must be run before performing any global modeling. Any notebook which performs forecasting with global models on unseen data requires the original modeling results, so notebook 16 relies on the results of notebook 9.2, for example. 
* After completing the use of one notebook, it is advisable to shut it down before moving onto the next notebook. Because there is a large amount of data and many of the models used in this repo are fairly complex, it is easy to eat up a lot of RAM without thinking about it. By shutting down each notebook before moving to the next one, this problem can be largely avoided. 
* Please note: this code was originially run on a machine with _64 GB_ RAM. I do not know how it will perform with less than this.

#### Running Notebooks 1-3

**If you are planning on running the code in Notebooks 1,2, or 3, please read the following**

The following steps are optional since the required samples of data needed to run the models are provided with the github repo. However, if you would like to start from scratch, I have a few thoughts. 
1. If you are planning to start from notebook 1, please note that this notebook takes a substantial amount of time (many hours) to run. The Portland-Vancouver API is finnicky, does not always respond, and the data returned may not match identically with the data provided with the repo. As such, I _do not_ recommend starting from this notebook
2. If you are planning to begin from notebook 2, data in the Data/Intermediate directory will be overwritten. This should not be a problem for running the following notebooks, but the raw data included with the repo is a sample of the full raw data utilized in my dissertation, and as such, overwriting the intermediate data may change some of the results in subseqeunt notebooks. The only real work that happens in notebook 2 is that all data is formatted into similar CSV files and linear interpolation is used for imputation. However, the linear interpolation was found to be inadequate and as such, the imputation results in notebook 2 are never used again.  
3. Of notebooks 1, 2, and 3, I would recommend beginning here. In this notebook, we actually impute missing data using a modified temporal medians method. However, this notebook can also be skipped if desired as the data which has been imputed is already included with the repo. 

#### Running Notebooks 4 and On

Once data has been imputed and saved in the Data/Processed directory, the more interesting work begins.

* Run notebook 4 to perform exploratory analysis on the data
* Run notebook 5 to perform the basic local modeling that is done in the paper (Naive, ARIMA)
* Run notebook 6 to perform the Prophet based local modeling
* Run notebook 7 to perform clustering
* Run notebook 8 to perform pooled regression (first global models)
* Run notebook 9.1 and 9.2 to build globla LightGBM models
* Run notebook 9.3 to build local LightGBM models
* Run notebook 10 to build global DeepAR models
* Notebooks 11-19 are used for the data from unseen sensors
    * The raw data for these sensors is provided with the repo
* Notebook 99 is used for some additional plots and EDA on the unseen sensor data
