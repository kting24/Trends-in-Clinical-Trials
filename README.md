# Trends in Clinical Trials
Clinical trials play a pivotal role in advancing medical research, shaping healthcare policies and improving patient outcomes worldwide. Understanding the global landscape of clinical trials provides valuable insights into emerging health challenges, treatment trends, and research priorities.

Key Objectives: This project analyzes and visualizes worldwide patterns, trends, and disparities in clinical trials. Ultimately, the project aims to provide a comprehensive view of the state of medical research world-wide.

Status: This project is in-progress as of March 14, 2024.

```python
# This Python 3 environment comes with many helpful analytics libraries installed
# It is defined by the kaggle/python Docker image: https://github.com/kaggle/docker-python
# For example, here's several helpful packages to load

import numpy as np # linear algebra
import pandas as pd # data processing, CSV file I/O (e.g. pd.read_csv)
import matplotlib.pyplot as plt # plotting
import os # file directory handling
import xml.etree.ElementTree as ET # create, parse, modify XML data

# Input data files are available in the read-only "../input/" directory
# For example, running this (by clicking run or pressing Shift+Enter) will list all files under the input directory

"""
import os
for dirname, _, filenames in os.walk('/kaggle/input'):
    for filename in filenames:
        print(os.path.join(dirname, filename))
"""
# You can write up to 20GB to the current directory (/kaggle/working/) that gets preserved as output when you create a version using "Save & Run All" 
# You can also write temporary files to /kaggle/temp/, but they won't be saved outside of the current session
```




    "\nimport os\nfor dirname, _, filenames in os.walk('/kaggle/input'):\n    for filename in filenames:\n        print(os.path.join(dirname, filename))\n"



# **Introduction**
Clinical trials play a pivotal role in advancing medical research, shaping healthcare policies and improving patient outcomes worldwide. Understanding the global landscape of clinical trials provides valuable insights into emerging health challenges, treatment trends, and research priorities.

**Key Objectives:**
This project analyzes and visualizes worldwide patterns, trends, and disparities in clinical trials. Ultimately, the project aims to provide a comprehensive view of the state of medical research world-wide.

**Status:** This project is in-progress as of March 14, 2024.

# **Part I. Importing the Dataset** 

**About the Dataset**:
This dataset is originally sourced from ClinicalTrials.gov and made available on Kaggle. ClinicalTrials.gov is maintained by the U.S. National Library of Medicine (NLM), and is a public database of clinical trial research studies and their results.The Kaggle dataset was last updated on May 8th, 2020 and contains 338k clinical trials. Each of these entries are stored in XML format, containing information on each studies such as the status, phase, eligibility criteria, and the health condition in question. 


```python
# Directory containing all the folders with the XML data files
main_directory = "/kaggle/input/all-clinical-trials"

# List to store extracted data
data_list = []

# Iterate through each folder (NCTXXXX) in the main directory
for folder in os.listdir(main_directory):
    folder_path = os.path.join(main_directory, folder) # ex:/all-clinical-trials/NCT0000
    if os.path.isdir(folder_path): 
        # Iterate through each XML file in the folder
        for file_name in os.listdir(folder_path):
            if file_name.endswith(".xml"):
                file_path = os.path.join(folder_path, file_name) #ex:/NCT0000/NCT0000102.xml
                if os.path.getsize(file_path) == 0:
                    continue  # Skip empty files
                tree = ET.parse(file_path) #ElementTree object: entire XML file
                root = tree.getroot() # root element: <clinical_study>

                # Extract data from XML tags
                nct_id = root.find(".//nct_id").text
                brief_title = root.find(".//brief_title").text
                overall_status = root.find(".//overall_status").text
                phase = root.find(".//phase").text if root.find(".//phase") is not None else None
                study_type = root.find(".//study_type").text if root.find(".//study_type") is not None else None
                has_expanded_access = root.find(".//has_expanded_access").text if root.find(".//has_expanded_access") is not None else None
                intervention_model = root.find(".//intervention_model").text if root.find(".//intervention_model") is not None else None
                primary_purpose = root.find(".//primary_purpose").text if root.find(".//primary_purpose") is not None else None
                masking = root.find(".//masking").text if root.find(".//masking") is not None else None
                condition = root.find(".//condition").text if root.find(".//condition") is not None else None
                intervention_name = root.find(".//intervention_name").text if root.find(".//intervention_name") is not None else None
                minimum_age = root.find(".//minimum_age").text if root.find(".//minimum_age") is not None else None
                maximum_age = root.find(".//maximum_age").text if root.find(".//maximum_age") is not None else None
                gender = root.find(".//gender").text if root.find(".//gender") is not None else None
                healthy_volunteers = root.find(".//healthy_volunteers").text if root.find(".//healthy_volunteers") is not None else None
                facility_name = root.find(".//facility/name").text if root.find(".//facility/name") is not None else None
                facility_city = root.find(".//facility/address/city").text if root.find(".//facility/address/city") is not None else None
                facility_state = root.find(".//facility/address/state").text if root.find(".//facility/address/state") is not None else None
                facility_country = root.find(".//facility/address/country").text if root.find(".//facility/address/country") is not None else None
                verification_date = root.find(".//verification_date").text if root.find(".//verification_date") is not None else None
                study_first_submitted = root.find(".//study_first_submitted").text if root.find(".//study_first_submitted") is not None else None
                study_first_posted = root.find(".//study_first_posted").text if root.find(".//study_first_posted") is not None else None
                last_update_submitted = root.find(".//last_update_submitted").text if root.find(".//last_update_submitted") is not None else None
                last_update_posted = root.find(".//last_update_posted").text if root.find(".//last_update_posted") is not None else None
                condition_browse = [elem.text for elem in root.findall(".//condition_browse/mesh_term")]
                intervention_browse = [elem.text for elem in root.findall(".//intervention_browse/mesh_term")]

                # Create a dictionary with the extracted data
                data = {
                    "NCT_ID": nct_id,
                    "Brief_Title": brief_title,
                    "Overall_Status": overall_status,
                    "Phase": phase,
                    "Study_Type": study_type,
                    "Has_Expanded_Access": has_expanded_access,
                    "Intervention_Model": intervention_model,
                    "Primary_Purpose": primary_purpose,
                    "Masking": masking,
                    "Condition": condition,
                    "Intervention_Name": intervention_name,
                    "Minimum_Age": minimum_age,
                    "Maximum_Age": maximum_age,
                    "Gender": gender,
                    "Healthy_Volunteers": healthy_volunteers,
                    "Facility_Name": facility_name,
                    "Facility_City": facility_city,
                    "Facility_State": facility_state,
                    "Facility_Country": facility_country,
                    "Verification_Date": verification_date,
                    "Study_First_Submitted": study_first_submitted,
                    "Study_First_Posted": study_first_posted,
                    "Last_Update_Submitted": last_update_submitted,
                    "Last_Update_Posted": last_update_posted,
                    "Condition_Browse": condition_browse,
                    "Intervention_Browse": intervention_browse
                }

                # Append the data to the list
                data_list.append(data)

# Create a DataFrame from the list of dictionaries
df = pd.DataFrame(data_list)
df.head()

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>NCT_ID</th>
      <th>Brief_Title</th>
      <th>Overall_Status</th>
      <th>Phase</th>
      <th>Study_Type</th>
      <th>Has_Expanded_Access</th>
      <th>Intervention_Model</th>
      <th>Primary_Purpose</th>
      <th>Masking</th>
      <th>Condition</th>
      <th>...</th>
      <th>Facility_City</th>
      <th>Facility_State</th>
      <th>Facility_Country</th>
      <th>Verification_Date</th>
      <th>Study_First_Submitted</th>
      <th>Study_First_Posted</th>
      <th>Last_Update_Submitted</th>
      <th>Last_Update_Posted</th>
      <th>Condition_Browse</th>
      <th>Intervention_Browse</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>NCT01051180</td>
      <td>Is Doppler Necessary in Haemorrhoidal Artery L...</td>
      <td>Terminated</td>
      <td>None</td>
      <td>Observational</td>
      <td>No</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>Piles</td>
      <td>...</td>
      <td>Poole</td>
      <td>Dorset</td>
      <td>United Kingdom</td>
      <td>April 2017</td>
      <td>January 15, 2010</td>
      <td>January 18, 2010</td>
      <td>April 3, 2017</td>
      <td>April 5, 2017</td>
      <td>[Hemorrhage]</td>
      <td>[]</td>
    </tr>
    <tr>
      <th>1</th>
      <td>NCT01056120</td>
      <td>Long Term Safety Profile of the PRO-Kinetic EN...</td>
      <td>Completed</td>
      <td>None</td>
      <td>Observational</td>
      <td>No</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>De Novo and Re-stenosed Coronary Artery Lesions</td>
      <td>...</td>
      <td>Bruck an der Mur</td>
      <td>NRW</td>
      <td>Austria</td>
      <td>January 2016</td>
      <td>January 25, 2010</td>
      <td>January 26, 2010</td>
      <td>January 29, 2016</td>
      <td>February 1, 2016</td>
      <td>[]</td>
      <td>[]</td>
    </tr>
    <tr>
      <th>2</th>
      <td>NCT01059903</td>
      <td>Study in Healthy Volunteers to Prove That Two ...</td>
      <td>Completed</td>
      <td>Phase 1</td>
      <td>Interventional</td>
      <td>No</td>
      <td>Crossover Assignment</td>
      <td>Basic Science</td>
      <td>None (Open Label)</td>
      <td>Healthy Volunteers</td>
      <td>...</td>
      <td>Moenchengladbach</td>
      <td>NRW</td>
      <td>Germany</td>
      <td>May 2012</td>
      <td>January 28, 2010</td>
      <td>February 1, 2010</td>
      <td>May 15, 2012</td>
      <td>May 22, 2012</td>
      <td>[]</td>
      <td>[Rotigotine]</td>
    </tr>
    <tr>
      <th>3</th>
      <td>NCT01058031</td>
      <td>Neural Correlates of PTSD Prevention With Mind...</td>
      <td>Completed</td>
      <td>N/A</td>
      <td>Interventional</td>
      <td>No</td>
      <td>Parallel Assignment</td>
      <td>Treatment</td>
      <td>None (Open Label)</td>
      <td>Stress Disorders, Posttraumatic</td>
      <td>...</td>
      <td>Decatur</td>
      <td>Georgia</td>
      <td>United States</td>
      <td>November 2013</td>
      <td>January 26, 2010</td>
      <td>January 28, 2010</td>
      <td>November 4, 2013</td>
      <td>November 5, 2013</td>
      <td>[Stress Disorders, Post-Traumatic]</td>
      <td>[]</td>
    </tr>
    <tr>
      <th>4</th>
      <td>NCT01056835</td>
      <td>Effects of PGI2 Analogue Use on the Developmen...</td>
      <td>Completed</td>
      <td>Phase 3</td>
      <td>Interventional</td>
      <td>No</td>
      <td>Parallel Assignment</td>
      <td>Prevention</td>
      <td>None (Open Label)</td>
      <td>Chronic Allograft Nephropathy</td>
      <td>...</td>
      <td>Seoul</td>
      <td>None</td>
      <td>Korea, Republic of</td>
      <td>May 2015</td>
      <td>January 24, 2010</td>
      <td>January 26, 2010</td>
      <td>May 24, 2015</td>
      <td>May 27, 2015</td>
      <td>[Kidney Diseases]</td>
      <td>[Epoprostenol, Tezosentan]</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 26 columns</p>
</div>



**Cleaning the Dataset**


```python
# common conditions
conditions = df[['Condition']]
conditions
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Condition</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Piles</td>
    </tr>
    <tr>
      <th>1</th>
      <td>De Novo and Re-stenosed Coronary Artery Lesions</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Healthy Volunteers</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Stress Disorders, Posttraumatic</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Chronic Allograft Nephropathy</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
    </tr>
    <tr>
      <th>103502</th>
      <td>Insomnia</td>
    </tr>
    <tr>
      <th>103503</th>
      <td>Diabetic Neuropathy, Painful</td>
    </tr>
    <tr>
      <th>103504</th>
      <td>Thyroid Neoplasm</td>
    </tr>
    <tr>
      <th>103505</th>
      <td>Gastroesophageal Reflux</td>
    </tr>
    <tr>
      <th>103506</th>
      <td>Orthomyxoviridae Infection</td>
    </tr>
  </tbody>
</table>
<p>103507 rows × 1 columns</p>
</div>


