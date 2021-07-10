**Problem Statement**
To build a classification methodology to determine whether a customer is placing a fraudulent insurance claim.

**Data Description**
The client will send data in multiple sets of files in batches at a given location. The data has been extracted from the census bureau. 
The data contains the following attributes:

**Features:**
months_as_customer: It denotes the number of months for which the customer is associated with the insurance company.
age: continuous. It denotes the age of the person.
policy_number: The policy number.
policy_bind_date: Start date of the policy.
policy_state: The state where the policy is registered.
policy_deductable: The amount paid out of pocket by the policy-holder before an insurance provider will pay any expenses.
policy_annual_premium: The yearly premium for the policy.
umbrella_limit: An umbrella insurance policy is extra liability insurance coverage that goes beyond the limits of the insured's homeowners, auto or watercraft insurance. It provides an additional layer of security to those who are at risk of being sued for damages to other people's property or injuries caused to others in an accident.
insured_zip: The zip code where the policy is registered.
insured_sex: It denotes the person's gender.
insured_education_level: The highest educational qualification of the policy-holder.
insured_occupation: The occupation of the policy-holder.
insured_hobbies: The hobbies of the policy-holder.
insured_relationship: Dependents on the policy-holder.
capital-gain: It denotes the monitory gains by the person.
capital-loss: It denotes the monitory loss by the person.
incident_date: The date when the incident happened.
incident_type: The type of the incident.
collision_type: The type of collision that took place.
incident_severity: The severity of the incident.
authorities_contacted: Which authority was contacted.
incident_state: The state in which the incident took place.
incident_city: The city in which the incident took place. 
incident_location: The street in which the incident took place.
incident_hour_of_the_day: The time of the day when the incident took place.
property_damage: If any property damage was done.
bodily_injuries: Number of bodily injuries.
Witnesses: Number of witnesses present.
police_report_available: Is the police report available.
total_claim_amount: Total amount claimed by the customer.
injury_claim: Amount claimed for injury
property_claim: Amount claimed for property damage.
vehicle_claim: Amount claimed for vehicle damage.
auto_make: The manufacturer of the vehicle
auto_model: The model of the vehicle. 
auto_year: The year of manufacture of the vehicle. 

T**arget Label:**
Whether the claim is fraudulent or not.
fraud_reported:  Y or N
Apart from training files, we also require a "schema" file from the client, which contains all the relevant information about the training files such as:
Name of the files, Length of Date value in FileName, Length of Time value in FileName, Number of Columns, Name of the Columns, and their datatype.

**Data Validation **
In this step, we perform different sets of validation on the given set of training files.  
 Name Validation- We validate the name of the files based on the given name in the schema file. We have created a regex pattern as per the name given in the schema file to use for validation. After validating the pattern in the name, we check for the length of date in the file name as well as the length of time in the file name. If all the values are as per requirement, we move such files to "Good_Data_Folder" else we move such files to "Bad_Data_Folder."

** Number of Columns**
- We validate the number of columns present in the files, and if it doesn't match with the value given in the schema file, then the file is moved to "Bad_Data_Folder."

**Name of Columns **
- The name of the columns is validated and should be the same as given in the schema file. If not, then the file is moved to "Bad_Data_Folder".

 The datatype of columns - The datatype of columns is given in the schema file. It is validated when we insert the files into Database. If the datatype is wrong, then the file is moved to "Bad_Data_Folder".


Null values in columns - If any of the columns in a file have all the values as NULL or missing, we discard such a file and move it to "Bad_Data_Folder".
Data Insertion in Database
 
1) Database Creation and connection - Create a database with the given name passed. If the database has already been created, open a connection to the database. 
2) Table creation in the database - Table with name - "Good_Data", is created in the database for inserting the files in the "Good_Data_Folder" based on given column names and datatype in the schema file. If the table is already present, then the new table is not created, and new files are inserted in the already present table as we want training to be done on new as well as old training files.     
3) Insertion of files in the table - All the files in the "Good_Data_Folder" are inserted in the above-created table. If any file has invalid data type in any of the columns, the file is not loaded in the table and is moved to "Bad_Data_Folder".

**Model Training **

1) Data Export from Db - The data in a stored database is exported as a CSV file to be used for model training.
2) Data Preprocessing   
Drop the columns not required for prediction.
For this dataset, the null values were replaced with ‘?’ in the client data. Those ‘?’ have been replaced with NaN values.
Check for null values in the columns. If present, impute the null values using the categorical imputer.
Replace and encode the categorical values with numeric values.
Scale the numeric values using the standard scaler.
3) Clustering - KMeans algorithm is used to create clusters in the preprocessed data. The optimum number of clusters is selected by plotting the elbow plot, and for the dynamic selection of the number of clusters, we are using "KneeLocator" function. The idea behind clustering is to implement different algorithms
The Kmeans model is trained over preprocessed data, and the model is saved for further use in prediction.
4) Model Selection – After the clusters have been created, we find the best model for each cluster. We are using two algorithms, “SVM” and "XGBoost". For each cluster, both the algorithms are passed with the best parameters derived from GridSearch. We calculate the AUC scores for both models and select the model with the best score. Similarly, the model is selected for each cluster. All the models for every cluster are saved for use in prediction.

**Prediction Data Description**

 The Client will send the data in multiple sets of files in batches at a given location. Data will contain the annual income of various persons. 
Apart from prediction files, we also require a "schema" file from the client, which contains all the relevant information about the training files such as:
Name of the files, Length of Date value in FileName, Length of Time value in FileName, Number of Columns, Name of the Columns and their datatype.

