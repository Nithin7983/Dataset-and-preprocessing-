# Dataset-and-preprocessing-

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from sklearn.preprocessing import StandardScaler
from sklearn.model_selection import train_test_split

df = pd.read_csv("titanic.csv")

print(df.head())
print(df.info())
print(df.isnull().sum())

df['Age'] = df['Age'].fillna(df['Age'].median())
df['Embarked'] = df['Embarked'].fillna(df['Embarked'].mode()[0])
if 'Cabin' in df.columns:
    df = df.drop('Cabin', axis=1)

df['Sex'] = df['Sex'].map({'male': 0, 'female': 1})
df = pd.get_dummies(df, columns=['Embarked'], drop_first=True)

scaler = StandardScaler()
if 'Fare' in df.columns:
    df[['Age', 'Fare']] = scaler.fit_transform(df[['Age', 'Fare']])
else:
    df[['Age']] = scaler.fit_transform(df[['Age']])

df = df.drop_duplicates()

if 'Fare' in df.columns:
    Q2 = df['Fare'].quantile(0.50)
    Q3 = df['Fare'].quantile(0.75)
    IQR = Q3 - Q2
    df = df[(df['Fare'] >= Q2 - 1.5 * IQR) & (df['Fare'] <= Q3 + 1.5 * IQR)]

columns_to_drop = ['Name', 'Ticket', 'PassengerId']
for col in columns_to_drop:
    if col in df.columns:
        df = df.drop(col, axis=1)

X = df.drop('Survived', axis=1)
y = df['Survived']

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

print("Data cleaning and preprocessing completed successfully.")