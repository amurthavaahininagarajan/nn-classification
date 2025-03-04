# Developing a Neural Network Classification Model

## AIM

To develop a neural network classification model for the given dataset.

## Problem Statement

An automobile company has plans to enter new markets with their existing products. After intensive market research, they’ve decided that the behavior of the new market is similar to their existing market.

In their existing market, the sales team has classified all customers into 4 segments (A, B, C, D ). Then, they performed segmented outreach and communication for a different segment of customers. This strategy has work exceptionally well for them. They plan to use the same strategy for the new markets.

You are required to help the manager to predict the right group of the new customers.

## Neural Network Model
![311115306-d9a235ab-e9f6-4af5-afb3-49fec6be4a64](https://github.com/amurthavaahininagarajan/nn-classification/assets/118679102/18bca2b1-5b3e-476d-931e-a675c90b97a1)


## DESIGN STEPS


1.Importing Libraries:
The code begins by importing necessary libraries such as pandas, scikit-learn, TensorFlow, seaborn, etc.


2.Data Loading and Preprocessing:
The dataset is loaded from a CSV file named 'customers.csv' into a pandas DataFrame. Data cleaning is performed by removing rows with missing values (dropna()). Encoding categorical variables using OrdinalEncoder and LabelEncoder. Dropping unnecessary columns like 'ID' and 'Var_1'. Calculating and visualizing correlation matrix using seaborn's heatmap. Performing a pairplot to visualize relationships between variables. Scaling numerical features using MinMaxScaler.


3.Model Building and Training:
The neural network model is defined using Keras Sequential API. The model architecture consists of several dense layers with ReLU activation and a softmax output layer. The model is compiled with categorical cross-entropy loss and Adam optimizer. Training is done using fit() method on the training data. Early stopping is implemented to prevent overfitting. Training history is stored in a DataFrame for visualization.

4.Model Evaluation:
Metrics such as loss and accuracy are plotted for both training and validation data. Confusion matrix and classification report are printed to evaluate the model's performance.

5.Model and Data Saving:
The trained model is saved as a HDF5 file. Necessary data objects like scaled training/test data, encoders, scalers, etc., are saved using pickle for future use.

6.Model Loading and Prediction:
The saved model and data objects are loaded. A single test input is predicted using the loaded model, and the result is printed.

This code essentially demonstrates a typical machine learning pipeline, including data preprocessing, model training, evaluation, and saving/loading for future use. It leverages TensorFlow/Keras for neural network implementation and scikit-learn for data preprocessing tasks.  

## PROGRAM

### Name: AMURTHA VAAHINI.KN
### Register Number: 212222240008

```python
import pandas as pd

from sklearn.model_selection import train_test_split
from tensorflow.keras.models import Sequential
from tensorflow.keras.models import load_model

from sklearn.model_selection import train_test_split
from tensorflow.keras.models import Sequential
from tensorflow.keras.models import load_model

from tensorflow.keras.layers import Dense
from tensorflow.keras.layers import Dropout
from tensorflow.keras.layers import BatchNormalization

import tensorflow as tf
import seaborn as sns

from tensorflow.keras.callbacks import EarlyStopping
from sklearn.preprocessing import MinMaxScaler
from sklearn.preprocessing import LabelEncoder
from sklearn.preprocessing import OneHotEncoder
from sklearn.preprocessing import OrdinalEncoder
from sklearn.metrics import classification_report,confusion_matrix

import pickle

import numpy as np
import matplotlib.pylab as plt

customer_df = pd.read_csv('customers.csv')
customer_df.head()
customer_df.columns
customer_df.dtypes
customer_df.shape
customer_df.isnull().sum()
customer_df_cleaned = customer_df.dropna(axis=0)
customer_df_cleaned.isnull().sum()
customer_df_cleaned.shape
customer_df_cleaned.dtypes
customer_df_cleaned['Gender'].unique()
customer_df_cleaned['Ever_Married'].unique()
customer_df_cleaned['Graduated'].unique()
customer_df_cleaned['Profession'].unique()
customer_df_cleaned['Spending_Score'].unique()
customer_df_cleaned['Var_1'].unique()
customer_df_cleaned['Segmentation'].unique()

categories_list=[['Male', 'Female'],
           ['No', 'Yes'],
           ['No', 'Yes'],
           ['Healthcare', 'Engineer', 'Lawyer', 'Artist', 'Doctor',
            'Homemaker', 'Entertainment', 'Marketing', 'Executive'],
           ['Low', 'Average', 'High']
           ]
enc = OrdinalEncoder(categories=categories_list)

customers_1 = customer_df_cleaned.copy()

customers_1[['Gender',
             'Ever_Married',
              'Graduated','Profession',
              'Spending_Score']] = enc.fit_transform(customers_1[['Gender',
                                                                 'Ever_Married',
                                                                 'Graduated','Profession',
                                                                 'Spending_Score']])



customers_1.dtypes
le = LabelEncoder()

customers_1['Segmentation'] = le.fit_transform(customers_1['Segmentation'])

customers_1.dtypes

customers_1 = customers_1.drop('ID',axis=1)
customers_1 = customers_1.drop('Var_1',axis=1)

customers_1.dtypes

# Calculate the correlation matrix
corr = customers_1.corr()

# Plot the heatmap
sns.heatmap(corr,
        xticklabels=corr.columns,
        yticklabels=corr.columns,
        cmap="BuPu",
        annot= True)

sns.pairplot(customers_1)

plt.figure(figsize=(10,6))
sns.countplot(customers_1['Family_Size'])

plt.figure(figsize=(10,6))
sns.boxplot(x='Family_Size',y='Age',data=customers_1)

plt.figure(figsize=(10,6))
sns.scatterplot(x='Family_Size',y='Spending_Score',data=customers_1)

plt.figure(figsize=(10,6))
sns.scatterplot(x='Family_Size',y='Age',data=customers_1)

customers_1.describe()

customers_1['Segmentation'].unique()

X=customers_1[['Gender','Ever_Married','Age','Graduated','Profession','Work_Experience','Spending_Score','Family_Size']].values

y1 = customers_1[['Segmentation']].values

one_hot_enc = OneHotEncoder()
one_hot_enc.fit(y1)

y1.shape
y = one_hot_enc.transform(y1).toarray()
y.shape
y1[0]
y[0]
X.shape

X_train,X_test,y_train,y_test=train_test_split(X,y,
                                               test_size=0.33,
                                               random_state=50)

X_train[0]

X_train.shape

scaler_age = MinMaxScaler()

scaler_age.fit(X_train[:,2].reshape(-1,1))

X_train_scaled = np.copy(X_train)
X_test_scaled = np.copy(X_test)

# To scale the Age column
X_train_scaled[:,2] = scaler_age.transform(X_train[:,2].reshape(-1,1)).reshape(-1)
X_test_scaled[:,2] = scaler_age.transform(X_test[:,2].reshape(-1,1)).reshape(-1)

from tensorflow.keras.callbacks import EarlyStopping

ai_brain = Sequential()
X_train.shape[1]
X_train_scaled.shape
ai_brain.add(Dense(6, activation = "relu", input_shape = (X_train.shape[1],)))
ai_brain.add(Dense(10, activation = "relu"))
ai_brain.add(Dense(10, activation = "relu"))
ai_brain.add(Dense(4, activation = "softmax"))
ai_brain.summary()

ai_brain.compile(optimizer='adam', loss = 'categorical_crossentropy', metrics=['accuracy'])
early_stop = EarlyStopping(monitor='val_loss', patience=2)
ai_brain.fit(x = X_train_scaled, y = y_train, epochs = 2000, batch_size = 32, validation_data = (X_test_scaled, y_test),)
metrics = pd.DataFrame(ai_brain.history.history)
metrics.head()
metrics[['loss','val_loss']].plot()

x_test_predictions = np.argmax(ai_brain.predict(X_test_scaled), axis=1)
x_test_predictions.shape
y_test_truevalue = np.argmax(y_test,axis=1)
y_test_truevalue.shape
print(confusion_matrix(y_test_truevalue,x_test_predictions))
print(classification_report(y_test_truevalue,x_test_predictions))

ai_brain.save('customer_classification_model.h5')
with open('customer_data.pickle', 'wb') as fh:
   pickle.dump([X_train_scaled,y_train,X_test_scaled,y_test,customers_1,customer_df_cleaned,scaler_age,enc,one_hot_enc,le], fh)
ai_brain = load_model('customer_classification_model.h5')
with open('customer_data.pickle', 'rb') as fh:
   [X_train_scaled,y_train,X_test_scaled,y_test,customers_1,customer_df_cleaned,scaler_age,enc,one_hot_enc,le]=pickle.load(fh)

x_single_prediction = np.argmax(ai_brain.predict(X_test_scaled[1:2,:]), axis=1)

print(x_single_prediction)
print(le.inverse_transform(x_single_prediction))




```

## Dataset Information
![311113694-7ae7a20b-aa96-4276-bac4-0f9d0379681a](https://github.com/amurthavaahininagarajan/nn-classification/assets/118679102/e4241f06-a45a-4026-9e85-d2a1d775d333)


## OUTPUT
### Training Loss, Validation Loss Vs Iteration Plot
![311115617-9c08926b-32ee-491a-b7ba-a3179380f458](https://github.com/amurthavaahininagarajan/nn-classification/assets/118679102/ebae80a4-fc1b-4762-b906-c51ee071c7b7)

### Classification Report
![311116487-febead3d-eead-4b14-9d9e-3525cc0a12b5](https://github.com/amurthavaahininagarajan/nn-classification/assets/118679102/9a2b1b28-5328-4b70-9463-ea287cc18a97)


### Confusion Matrix
![311116565-ba4ab59f-a9b0-4587-8a87-f2689286010b](https://github.com/amurthavaahininagarajan/nn-classification/assets/118679102/75a6e10f-5045-40a1-9145-81fba31b3e41)


### New Sample Data Prediction
![311249552-88d11da1-c27e-4348-a59c-4bb0eb4c724e](https://github.com/amurthavaahininagarajan/nn-classification/assets/118679102/fac61057-2ca9-489b-b240-b78155daed75)


## RESULT
Therefore , a neural network classification model has been developed for the given dataset to predict the right group of new customers.
