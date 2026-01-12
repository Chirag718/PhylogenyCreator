# PhylogenyCreator

This repository contains code to detect breaks and fusions in a HiC file through machine learning, and use this feature data to reconstruct phylogenetic trees. There were two different kinds of models developed for this task:

**Distance Classifier:** This model predicted the range of the distance between the regions in the center of its input data (e.g. Between 2 and 5 MB)

**Binary Classifier:** This model predicted a probability there was a break between the regions in the center of its input data.

The Distance Classifier is the most successful model as of September 2024.

## Guide
This guide provides a breakdown of what each notebook provides. 
### Distance Notebooks
#### DataCreation.ipynb:
* Constructs interchromosomal break matrices
* Constructs intrachromosomal break matrices with the break distance in a specific range
* Constructs no break matrices
* Saves all n by n matrices from a HiC file’s diagonal (n can be any number)
#### Training.ipynb:
* Loading and preprocessing training, validation, and testing data
* Visualizing training examples
* Train models with callbacks 
* Evaluating the model with confusion matrices, accuracy, loss, etc.
* Visualizing correct and incorrect examples
* Saliency map on the model’s predictions
#### Deployment.ipynb:
* Removing low-read bins from input matrices into the model
* Model deployment for breaks
* Filtering output bed files and combined handling of bed files to compare features across files
* Heatmaps of the presence of breaks and fusions in different files
* Building and visualizing fusions and predicting on them
* Determining most likely fusions
* Reconstructing phylogeny based on breaks and fusions
#### DistanceMetrics.ipynb:
* Constructing interchromosomal break matrices
* Constructing intrachromosomal break matrices with a specific break distance
* Creating Distance vs. Probability graphs for the model
* Creating Distance vs. Saliency graphs for the model
#### Visualizer.ipynb:
* Visualizing and predicting on continuous regions and fusions
* Visualizing and predicting on continuous regions and fusions after removing low-read bins
### Binary Classifier Notebooks:
#### DataCreation.ipynb: 
* Constructs interchromosomal break matrices with specific modifications possible
* Constructs intrachromosomal break matrices with the break distance in a specific range with specific modifications possible
* Constructs no break matrices
* Saves all n by n matrices from a HiC file’s diagonal (n can be any number)
#### Training.ipynb: 
* Loading and preprocessing training, validation, and testing data
* Visualizing training examples
* Train models with callbacks
* Evaluating the model with confusion matrices, accuracy, loss, etc.
* ROC curves
* Visualizing correct and incorrect examples
#### Deployment.ipynb:
* Model deployment for breaks
* Filtering output bed files and combined handling of bed files to compare features across files
* Heatmaps of the presence of breaks and fusions in different files
* Heatmap of the probability of fusions in different files
* Building and visualizing fusions and predicting on them
* Determining most likely fusions
* Reconstructing phylogeny based on breaks and fusions
#### DistanceVsProbability.ipynb:
* Constructing interchromosomal break matrices
* Constructing intrachromosomal break matrices with a specific break distance
* Creating Distance vs. Probability graphs for the model
## Pipeline
### Distance Classifier:
#### Step 1: 
Use the _DataCreation.ipynb_ notebook to create training examples. To run, fill the _max_distance_, _WIDTH_, _res_, and _ds_scale_ variables. Then, fill the _folder_ variable to indicate where the examples should be saved. Finally, fill the _folder_root_ variable to indicate where the data that the model will be deployed on will be saved.
#### Step 2: 
Use the _Training.ipynb_ notebook to train the model. To run, fill the _WIDTH_ and _res_ variables. Then, fill the _folder_ and _bins_ variables to indicate where the training examples should be drawn from and the ranges the model will predict from. Finally, indicate the name the model will be saved by in the _od.save()_ function. 

#### Step 3: 
Use the _Deployment.ipynb_ notebook to deploy the model on breaks and fusions and construct the final phylogeny. To run, fill the  _WIDTH_, _res_, and _model_ variables. Then, fill the _folder_ variable to indicate the data the model will be deployed on. Finally, fill the _folder_results_ variable to indicate where the model's results will be saved.
#### Step 4 (Optional): 
Use _DistanceMetrics.ipynb_ to assess model metrics against break distance. To run, fill the  _WIDTH_, _res_, and _model_ variables. Then, fill the _labels_ variable to indicate the ranges the model will predict from.
#### Step 5 (Optional): 
Use _Visualizer.ipynb_ to analyze model predictions on custom-built breaks and fusions. To run, fill the  _WIDTH_, _res_, and _model_ variables. Then, fill the _labels_ variable to indicate the ranges the model will predict from.
### Binary Classifier:
#### Step 1: 
Use the _DataCreation.ipynb_ notebooks to create training examples. To run, fill the _max_distance_, _WIDTH_, _res_, and _ds_scale_ variables. Then, fill the _folder_ variable to indicate where the results should be saved. Finally, fill the _folder_root_ variable to indicate where the data that the model will be deployed on will be saved.
#### Step 2: 
Use the _Training.ipynb_ notebooks to train the model. To run, fill the _WIDTH_, _res_, and _break_distance_ variables. Then, fill the _folder_ and _bins_ variables to indicate where the training examples should be drawn from and each kind of training data. Finally, indicate the name the model will be saved by in the _od.save()_ function. 
#### Step 3: 
Use the _Deployment.ipynb_ notebook to deploy the model on breaks and fusions and construct the final phylogeny. To run, fill the  _WIDTH_, _res_, and _model_ variables. Then, fill the _folder_ variable to indicate the data the model will be deployed on. Finally, fill the _folder_results_ variable to indicate where the model's results will be saved.
#### Step 4 (Optional): 
Use _DistanceVsProbability.ipynb_ to assess model metrics against break distance. To run, fill the  _WIDTH_, _res_, and _model_ variables. 
## NOTES: 
* These notebooks are primarily designed to handle observed data but can be easily modified to work with other types of data as well. Additionally, all notebooks contain functions to aid in the use of observed/expected (oe) data.
* The Distance Classifier predicts a vector, while the Binary Classifier predicts a scalar. Accordingly, the functions used for each kind of classifier are slightly different but can be easily modified to work for the other.
* The variables mentioned above must be consistent with each other. For example, if loading a training folder of examples with dimensions 40 by 40 and constructed at a resolution of 50 KB, _WIDTH_ must be set to 40 and _res_ to 50000. Another example is if loading a model to predict from 4 different ranges, _labels_ must be set to those 4 ranges.
* All variables mentioned above have default values set in the code
## Data Structuring Format
### Training folder: 
A training folder will contain subfolders that are each named after the file the data in that subfolder was created from. Inside these subfolders, the data will take the name of each kind of training example the model will be trained on. An example for the Distance Classifier would be _2_to_5MB.npy_ and an example for the Binary Classifier would be _Type1_data.npy_. Additionally, the Binary Classifier will have labels for its data with names like _Type1_label.npy_
### Deployment Folder:
A deployment folder will contain subfolders that are each named after the file the data in that subfolder was created from. Inside these subfolders, the data will take the name of each chromosome in the file (e.g. _CM000377.2 npy_)
### Results Folder:
A results folder will contain the results of the hic files the model was deployed on with the output files being named the hic file followed by .bed (e.g. _HIC1024_deep.hic.bed_). These folders also contain _fusion_matrix.npy_, the fusion matrix constructed from the results of the model deployment on the hic files. Finally, the folder contains _phylogeny.dot_, which contains the final phylogeny.
