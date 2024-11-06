# Bank-Customer-Churn-Model
This project uses machine learning to predict customer churn for a bank, helping identify at-risk customers. By analyzing factors like account activity, tenure, and demographics, we build a model to classify customers as likely to "churn" or "retain." This allows for targeted retention strategies, improving customer satisfaction and reducing churn.

**Title Of Project**:Bank-Customer-Churn-Model
**Import Library**
import pandas as pd

import numpy as np

import matplotlib.pyplot as plt

import seaborn as sns

df=pd.read_csv('https://raw.githubusercontent.com/YBI-Foundation/Dataset/refs/heads/main/Bank%20Churn%20Modelling.csv')

df.head()

df.info()

df.duplicated('CustomerId').sum()

df=df.set_index('CustomerId')

df.info()

## Encoding

df['Geography'].value_counts()

df.replace({'Geography':{'France':2,'Germany':1,'Spain':0}},inplace=True)

df['Gender'].value_counts()

df.replace({'Gender':{'Male':0,'Female':1}},inplace=True)

df['Num Of Products'].value_counts()

df.replace({'Num Of Products':{1:0,2:1,3:1,4:1}},inplace=True)

df['Has Credit Card'].value_counts()

df['Is Active Member'].value_counts()

df.loc[(df['Balance']==0),'Churn'].value_counts()

df['Zero Balance']=np.where(df['Balance']>0,1,0)

df['Zero Balance'].hist()

df.groupby(['Churn','Geography']).count()

## Define Label and Features

df.columns

x=df.drop(['Surname','Churn'],axis=1)

y=df['Churn']

x.shape,y.shape

df['Churn'].value_counts()

sns.countplot(x='Churn',data=df);

## Random Under Sampling

from imblearn.under_sampling import RandomUnderSampler

rus=RandomUnderSampler(random_state=2529)

x_rus,y_rus=rus.fit_resample(x,y)

x_rus.shape,y_rus.shape,x.shape,y.shape

y.value_counts()

y_rus.value_counts()

y_rus.plot(kind='hist')

## Random Over Sampling

from imblearn.over_sampling import RandomOverSampler

ros=RandomOverSampler(random_state=2529)

x_ros,y_ros=ros.fit_resample(x,y)

x_ros.shape,y_ros.shape,x.shape,y.shape

y.value_counts()

y_ros.value_counts()

y_ros.plot(kind='hist')

## Train Test Split

from sklearn.model_selection import train_test_split

## Split Original Data

x_train,x_test,y_train,y_test=train_test_split(x,y,test_size=0.3,random_state=25)

## Split Random Under Sample Data

x_train_rus,x_test_rus,y_train_rus,y_test_rus=train_test_split(x_rus,y_rus,test_size=0.3,random_state=25)

## Split Random Over Sample Data

x_train_ros,x_test_ros,y_train_ros,y_test_ros=train_test_split(x_ros,y_ros,test_size=0.3,random_state=25)

## Standradize Features

from sklearn.preprocessing import StandardScaler

sc=StandardScaler()

## Standradize Original data

x_train[['CreditScore','Age','Tenure','Balance','Estimated Salary']]=sc.fit_transform(x_train[['CreditScore','Age','Tenure','Balance','Estimated Salary']])

x_test[['CreditScore','Age','Tenure','Balance','Estimated Salary']]=sc.fit_transform(x_test[['CreditScore','Age','Tenure','Balance','Estimated Salary']])

## Standardize Random Over Sample Data

x_train_ros[['CreditScore','Age','Tenure','Balance','Estimated Salary']]=sc.fit_transform(x_train_ros[['CreditScore','Age','Tenure','Balance','Estimated Salary']])

x_test_ros[['CreditScore','Age','Tenure','Balance','Estimated Salary']]=sc.fit_transform(x_test_ros[['CreditScore','Age','Tenure','Balance','Estimated Salary']])

## Standardize Random Under Sample Data

x_train_rus[['CreditScore','Age','Tenure','Balance','Estimated Salary']]=sc.fit_transform(x_train_rus[['CreditScore','Age','Tenure','Balance','Estimated Salary']])

x_test_rus[['CreditScore','Age','Tenure','Balance','Estimated Salary']]=sc.fit_transform(x_test_rus[['CreditScore','Age','Tenure','Balance','Estimated Salary']])

## Support Vector Machine Classifier

from sklearn.svm import SVC

svc=SVC()

svc.fit(x_train,y_train)

y_pred=svc.predict(x_test)

## Model Accuracy

from sklearn.metrics import confusion_matrix,classification_report

confusion_matrix(y_test,y_pred)

print(classification_report(y_test,y_pred))

## Hyperparameter Tuning

from sklearn.model_selection import GridSearchCV

param_grid={'C':[0.1,1,10],
            'gamma':[1,0.1,0.01],
            'kernel':['rbf'],
            'class_weight':['balanced']}

grid=GridSearchCV(SVC(),param_grid,refit=True,verbose=2,cv=2)
grid.fit(x_train,y_train)

print(grid.best_estimator_)

grid_pregdictions=grid.predict(x_test)

confusion_matrix(y_test,grid_predictions)

print(classification_report(y_test,grid_predictions))

## Model with Random Under Sampling

svc_rus=SVC()

svc_rus.fit(x_train_rus,y_train_rus)

y_pred_rus=svc_rus.predict(x_test_rus)

## Model Accuracy

from sklearn.metrics import confusion_matrix,classification_report

confusion_matrix(y_test_rus,y_pred_rus)

print(classification_report(y_test_rus,y_pred_rus))

## Hyperparameter Tunning

param_grid={'C':[0.1,1,10],
            'gamma':[1,0.1,0.01],
            'kernel':['rbf'],
            'class_weight':['balanced']}

grid_rus=GridSearchCV(SVC(),param_grid,refit=True,verbose=2,cv=2)
grid_rus.fit(x_train_rus,y_train_rus)

print(grid_rus.best_estimator_)

grid_predictions_rus=grid_rus.predict(x_test_rus)

confusion_matrix(y_test_rus,grid_predictions_rus)

print(classification_report(y_test_rus,grid_predictions_rus))

## Model with Random Over Sampling

svc_ros=SVC()

svc_ros.fit(x_train_ros,y_train_ros)

y_pred_ros=svc_ros.predict(x_test_ros)

## Model Accuarcy

confusion_matrix(y_test_ros,y_pred_ros)

print(classification_report(y_test_ros,y_pred_ros))

## Hyperparameter Tunning

param_grid={'C':[0.1,1,10],
            'gamma':[1,0.1,0.01],
            'kernel':['rbf'],
            'class_weight':['balanced']}

grid_ros=GridSearchCV(SVC(),param_grid,refit=True,verbose=2,cv=2)
grid_ros.fit(x_train_ros,y_train_ros)

print(grid_ros.best_estimator_)

grid_predictions_ros=grid_ros.predict(x_test_ros)

confusion_matrix(y_test_ros,grid_predictions_ros)

print(classification_report(y_test_ros,grid_predictions_ros))

## Lets Compare

print(classification_report(y_test,y_pred))

print(classification_report(y_test,grid_predictions))

print(classification_report(y_test_rus,y_pred_rus))

print(classification_report(y_test_rus,grid_predictions_rus))

print(classification_report(y_test_ros,y_pred_ros))

print(classification_report(y_test_ros,grid_predictions_ros))

**Explaination** :The Bank Customer Churn Model project aims to predict which customers are likely to leave the bank by analyzing historical data. It focuses on identifying key factors driving churn, enabling the bank to implement targeted retention strategies. By reducing churn, the project enhances customer loyalty, improves profitability, and provides insights for better customer service and product offerings.
