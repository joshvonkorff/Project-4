Summary:

In the Microsoft Malware Kaggle competition, the data instances represent individual computers that may or may not be infected by malware.  The HasDetections target variable assesses the presence or absence of malware, as judged by Microsoft.  The goal of the competition is to use 82 features to predict the target variable.  Some examples of the features include:

* AVProductsEnabled (are antivirus products enabled?)
* Firewall (is windows firewall enabled?)
* Census_ProcessorCoreCount (number of logical cores in the processor)
* Census_OSVersion (numeric OS version, such as 10.0.10130.0)

I have included two notebooks in the repository: JVK-MS-Malware-Fit and JVK-MS-Malware-Predict.  The Fit notebook reads in the data file and fits it to a model, then saves the model to a pickle file.  The Predict notebook reads the pickle file and uses it to make a prediction.  The reason for the separation between the Predict and Fit files is that the process is very RAM intensive.

The Fit notebook performs the following operations:

F1. Read a random selection of rows from a file.  

I chose to read 5% of the data file and throw out the rest, due to RAM constraints and due to the large number of features.  I found that when I increased or decreased this fraction, the fit to the target variable did not change very much.

F2. Make dummy columns.

First, columns with decimal point-separated values (like 10.0.10130.0 in the example above) are 

expanded into their components.
Next, for each “object” (non-numerical) column that was not expanded, dummy columns are made for each of the four most common values (or less, if there are less than four possible values.)

F3. Scale and impute:

Standardize the data, then fill the nans (not a number) with zero.

F4. Find strongest correlations.

Find Pearson’s R correlations between all pairs of columns.  Pick the 100 strongest correlations, and append the products of these pairs of columns as interaction terms.

F5. Random Forest

Apply a Random Forest Classifier with 300 estimators.  (I also tried Logistic Regression.)

F6. Save results to a file.

The saved results includes the Random Forest model as well as a list of all of the columns that we constructed.  This will enable us to reconstruct the same columns during the prediction step.

The Predict notebook performs the following operations:

P1. Load results saved in F6.

P2. Reconstruct the same data structure using the new data.  

I.e. we want a data structure with the same dummy columns and the same 100 interaction terms.  This requires remembering which dummy and interaction features we generated before.  If we were to re-generate the dummies and interactions from the new data, we might get different features and we could not apply the same Random Forest classifier.

P3. Make the predictions.

