import pandas as pd
import numpy as np
from pgmpy.models import DiscreteBayesianNetwork as BayesianNetwork
from pgmpy.estimators import MaximumLikelihoodEstimator
from pgmpy.inference import VariableElimination

# Step 1: Define the structure of the Bayesian Network
# 'Study_Hours' and 'Attendance' are parents of 'Pass'
model_student = BayesianNetwork([
    ('Study_Hours', 'Pass'),
    ('Attendance', 'Pass')
])

# Step 2: Create a Sample Dataset for Student Performance
# Let's assume: higher study hours and attendance increase the probability of passing
num_students = 200

data_student_dict = {
    'Study_Hours': np.random.choice(['Low', 'Medium', 'High'], num_students, p=[0.2, 0.4, 0.4]),
    'Attendance': np.random.choice(['Low', 'Medium', 'High'], num_students, p=[0.1, 0.3, 0.6]),
    'Pass': np.random.choice(['No', 'Yes'], num_students, p=[0.5, 0.5]) # Initial random distribution
}

df_student = pd.DataFrame(data_student_dict)

# Introduce dependencies: If Study_Hours and Attendance are high, Pass is more likely 'Yes'
# This is a simplified way to create a dataset with some correlation for demonstration.
for i in range(num_students):
    if df_student.loc[i, 'Study_Hours'] == 'High' and df_student.loc[i, 'Attendance'] == 'High':
        df_student.loc[i, 'Pass'] = np.random.choice(['Yes', 'No'], p=[0.9, 0.1])
    elif df_student.loc[i, 'Study_Hours'] == 'Medium' and df_student.loc[i, 'Attendance'] == 'Medium':
        df_student.loc[i, 'Pass'] = np.random.choice(['Yes', 'No'], p=[0.6, 0.4])
    elif df_student.loc[i, 'Study_Hours'] == 'Low' or df_student.loc[i, 'Attendance'] == 'Low':
        df_student.loc[i, 'Pass'] = np.random.choice(['Yes', 'No'], p=[0.3, 0.7])

# Display the first few rows of the generated dataset
print("Sample Student Data:")
display(df_student.head())

# Step 3: Train the Bayesian Network
model_student.fit(df_student, estimator=MaximumLikelihoodEstimator)

# Step 4: Perform Inference
inference_student = VariableElimination(model_student)

# Step 5: Query the model for a prediction
# Example: Predict 'Pass' for a student with 'High' Study Hours and 'High' Attendance
evidence_student = {'Study_Hours': 'High', 'Attendance': 'High'}
result_student = inference_student.query(variables=['Pass'], evidence=evidence_student)

print(f"\nProbability of Passing given {evidence_student}:")
print(result_student)

# Example: Predict 'Pass' for a student with 'Low' Study Hours and 'Low' Attendance
evidence_student_low = {'Study_Hours': 'Low', 'Attendance': 'Low'}
result_student_low = inference_student.query(variables=['Pass'], evidence=evidence_student_low)

print(f"\nProbability of Passing given {evidence_student_low}:")
print(result_student_low)
