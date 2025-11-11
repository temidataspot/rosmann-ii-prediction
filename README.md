# Rossmann Sales In-Sample Prediction Pipeline – Azure ML Designer [I]

![Pipeline Banner](https://dummyimage.com/1200x300/0078d4/ffffff&text=Rossmann+Sales+In-Sample+Prediction+Pipeline)

---

## Project Overview

This repository contains an **end-to-end machine learning pipeline** for predicting Rossmann store sales using **Azure Machine Learning Designer**.  

The purpose of this project is to:

1. Prepare and clean the Rossmann sales dataset.  
2. Build, train, and evaluate a **Linear Regression** model using Azure ML Designer.  
3. Score the dataset to generate in-sample predictions.  
4. Download the scored dataset (`.parquet`) and perform **visual analysis in Python**.  
5. Validate **in-sample predictions** to check model accuracy before forecasting future sales.  

---

## 1. Setting Up Azure ML Workspace

1. Navigated to [Azure Machine Learning Studio](https://ml.azure.com).   
2. **Create a Workspace**:
    Set up the following:
   - Resource Group  
   - Workspace Name  
   - Region: `East US`  
3. Created a **Compute Cluster** for Designer experiments:
   - Go to **Compute → Compute Clusters → + New**  
   - Choose VM size, e.g., `Standard_D3_v2`  
   - Set **Minimum/Maximum nodes**  
   - Click **Create**

![Compute Cluster Example](https://dummyimage.com/800x400/cccccc/000000&text=Azure+ML+Compute+Cluster)

---

## 2. Creating the Designer Pipeline

1. Go to **Designer → + Create new pipeline**.  
2. **Upload Dataset**:
   - Click **+ Dataset → From local files**  
   - Uploaded `rossmann_clean.csv`  
   - Previewed the dataset to check columns and formatting  

3. **Edit Metadata**:
   - Dragged the **Edit Metadata** module to canvas
   - Connected my uploaded dataset (`rossmann_clean.csv`) to its input
   - Configured the module:
   - **Columns to Edit**: Selected columns to modify.
   - **Data Type**: Ensured numeric columns (e.g., `Sales`)
   - **Columns to Delete**: Removed irrelevant columns

3. **Select Columns in dataset**:
   - Dragged the **Select Columns in dataset** module to canvas
   - Selected columns to include or exclude  
   - Confirmed data types (e.g., `Sales` as numeric, `Date` as datetime)
   
4. **Partition and Sample**:
   - Before splitting the dataset into training and testing sets, it is important to **partition and sample** the data to ensure a representative and manageable dataset.
   - Dragged the **Partition and Sample module** from the Azure ML Designer toolbox to the canvas
   - Connected the cleaned dataset (output from `Edit Metadata`) to the input of `Partition and Sample`
   - Configured the module settings 
   - This helps reduce computation time during training and ensures balanced representation of different stores, departments, and holidays.

5. **Split Data**:
   - Dragged **Split Data** module  
   - Connect it to the output of `Partition and Sample` 
   - Set **fraction** for training/testing (e.g., 0.8/0.2)  
   - Chose a random seed for reproducibility

5. **Train Model**:
   - Drag **Linear Regression** module to canvas
   - Connect **training split** as input  
   - Connect **Linear Regression** as model  

6. **Score Model**:
   - Drag **Score Model** module to canvas
   - Connect **trained model** and **test split**  
   - Outputs `Scored Dataset: DataFrameDirectory`
     
![Data](https://github.com/temidataspot/rosmann-ii-prediction/blob/main/model_score_access_ii.png) ![Data2](https://github.com/temidataspot/rosmann-ii-prediction/blob/main/model_score_access.png)

7. **Evaluate Model**:
   - Drag **Evaluate Model** module  
   - Connect **scored dataset** from Score Model  

**Model Pipeline Architecture:**

![Designer Canvas](https://github.com/temidataspot/rosmann-ii-prediction/blob/main/model_archi.png)

---

## 3. Submitting the Pipeline

1. Clicked **Configure → Submit**  
2. Selected **compute cluster**  
3. Wait for the pipeline to run  
4. **Green checkmarks** indicate module completion as seen below:

![ModelConfirmed](https://github.com/temidataspot/rosmann-ii-prediction/blob/main/model_checked.png)

**Metrics Output**:

| Metric                     | Value      |
|-----------------------------|-----------|
| Relative Absolute Error   | 0.19    |
| Relative Squared Error | 0.04  |
| Coefficient of Determination (R²)| 0.9556 |

![Metrics](https://github.com/temidataspot/rosmann-ii-prediction/blob/main/model_metrics.png)
---

## 4. Downloading the Scored Dataset

After pipeline completion:

1. Go to **Jobs → Select pipeline run**  
2. Clicked **Score Model → Access data**  
3. Downloaded the `.parquet` file (e.g., `data.dataset.parquet`)

![Artifact](https://github.com/temidataspot/rosmann-ii-prediction/blob/main/model_artifact_download.png)

> Note: In Azure ML Designer v2, outputs are stored as **artifacts**, not direct CSVs.  

---

## 5. Loading the Scored Dataset in Python

1. Open **VS Code** or **Jupyter Notebook**  
2. Install dependencies if needed:

```bash
!pip install pandas pyarrow fastparquet matplotlib seaborn
