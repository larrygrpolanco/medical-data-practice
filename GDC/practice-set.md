# **Data Management Practice Sets: TCGA Data & REDCap Simulation**

First, a quick general tip: For each TCGA project (THCA, HNSC), the different TSV files (clinical, follow_up, pathology_detail, exposure, family_history) represent different facets of information about the same set of patients. They'll almost certainly be linkable through a common patient identifier (often something like case_submitter_id or bcr_patient_barcode). Your first mini-task is to **confirm this linking ID in all files**.

Getting a trial of REDCap is an excellent idea. It'll make the "Simulate REDCap Project Creation" steps very tangible. If you haven't already, actively seek out a free trial or demo instance of REDCap. Many academic institutions offer this.

Here are some practice ideas, designed to help you learn about the data and how it might be used:

**Overarching Goals for Your Practice:**

- **Understand Data Relationships:** How do these separate files connect? (The patient ID is key).
- **Familiarize Yourself with Common Clinical Variables:** What gets measured/recorded?
- **Simulate Data Extraction for Specific Needs:** How would you pull together data to answer a researcher's question?
- **Think in REDCap Terms:** How would you design a REDCap project to collect or house this information?
- **Identify Potential Data Quality Issues:** Missing data, inconsistencies (though TCGA is pretty clean).

## **Practice Scenario 1: Basic Patient Cohort & Demographics**

**Simulated Researcher Request:** "I want to get a basic overview of the patients in the TCGA-THCA cohort. Can you give me a list of patients with their age at diagnosis, gender, and race? Also, let's note their vital status and last follow-up date."

**Files to Use:**

- clinical.tsv: Likely contains age at diagnosis, gender, race, vital status.
- follow_up.tsv: Likely contains dates of follow-up and vital status updates.

### **Practice Steps & Educational Guidance:**

1. **Explore** clinical.tsv**:**
    - Identify columns for **patient ID**, **age at initial diagnosis**, **gender**, **race**, **vital status**.
    - Load this into Excel/Python/R. How many unique patients are there?
    - **Guiding Question:** What data types do you anticipate for each of these columns (e.g., numeric, text, categorical)? How might missing values be represented in this file?
2. **Explore** follow_up.tsv**:**
    - Identify columns for **patient ID**, **follow-up date**, **vital status at follow-up**.
    - Notice that there might be multiple follow-up entries per patient. How would you get the **latest follow-up information**? (This is a common task).
    - **Guiding Question:** Why would a patient have multiple follow-up entries? What are the implications for data analysis if you just pick an arbitrary entry instead of the _latest_ one?
3. **Merge/Link Data (Conceptually or Actually):**
    - How would you combine the demographic information from clinical.tsv with the latest follow-up date from follow_up.tsv for each patient? (In Pandas/R, this is a **merge or join operation** on the patient ID).
    - **Guiding Question:** What type of join would be most appropriate here (e.g., inner, left, right, outer)? Why? If a patient is in clinical.tsv but not in follow_up.tsv, how would your chosen join handle that?

### **REDCap Simulation:**

- If you were designing a REDCap project for a new thyroid cancer study, what forms would you create?
  - Maybe a "**Demographics**" form (Patient ID, Date of Birth, Gender, Race, Ethnicity).
  - Maybe a "**Diagnosis**" form (Date of Diagnosis, Age at Diagnosis - this could be calculated in REDCap if you have DOB and Diagnosis Date).
  - A repeating "**Follow-Up**" form (Follow-Up Date, Vital Status, Disease Status).
- What **field types** would you use for each (text, dropdown, date, radio buttons)?
- **REDCap Guiding Tip:** REDCap's "Data Dictionary" is where you define all your forms and fields. Think about the field names, labels, validation rules, and choices for dropdowns/radio buttons.
- **REDCap Guiding Question:** For "Vital Status," would you use a radio button or a dropdown? What are the pros and cons of each for this specific field? How would you handle a **calculated field** like "Age at Diagnosis" in REDCap? (Hint: Look into "Smart Variables" or "Calculated Fields" in REDCap documentation).

**Learning Focus:** Identifying core demographic and outcome variables, handling one-to-many relationships (one patient, multiple follow-ups), thinking about REDCap form structure.

## **Practice Scenario 2: Focus on Diagnosis & Staging Details**

**Simulated Researcher Request:** "For the TCGA-HNSC project, I need to find all patients diagnosed with Stage III or Stage IV cancer. I also want to know their specific tumor site (e.g., oral cavity, larynx) and histological type."

**Files to Use:**

- clinical.tsv: Usually contains AJCC staging information (pathologic stage, clinical stage), primary diagnosis site, and histological type.
- Potentially pathology_detail.tsv if the clinical.tsv isn't detailed enough or if specific pathology report findings are needed (though start with clinical.tsv).

### **Practice Steps & Educational Guidance:**

1. **Explore** clinical.tsv**:**
    - Find columns related to: **Patient ID**, **AJCC pathologic stage** (or clinical stage if pathologic is missing), **tumor site/primary diagnosis**, and **histological type**.
    - What are the different categories for tumor stage? How is it coded?
    - What are the common tumor sites listed?
    - **Guiding Question:** Why is it important to distinguish between "clinical stage" and "pathologic stage"? Which one is generally considered more definitive?
2. **Filter Data:**
    - How would you select only patients with **Stage III or Stage IV cancer**?
    - **Guiding Question:** What potential challenges might arise if the staging data is inconsistent (e.g., some patients have only clinical stage, others only pathologic)? How would you prioritize or combine them for this request?
3. **Summarize:**
    - For the selected Stage III/IV patients, how many fall into each tumor site category?
    - **Guiding Question:** Besides a count, what other summary statistics might be useful for the researcher (e.g., percentages)?

### **REDCap Simulation:**

- In your "Diagnosis" form in REDCap (from Scenario 1), how would you add fields for:
  - **Primary Tumor Site** (dropdown with common HNSC sites).
  - **Histological Type** (dropdown).
  - **AJCC Clinical Stage** (dropdown: I, II, III, IV, Not Applicable, Unknown).
  - **AJCC Pathologic Stage** (dropdown).
  - You might also have fields for T, N, M staging components.
- **REDCap Guiding Tip:** For dropdowns, consider creating a "codebook" or list of predefined choices. This ensures data consistency. For example, for "AJCC Clinical Stage," you might have choices like 1, Stage I | 2, Stage II | 3, Stage III | 4, Stage IV | 99, Not Applicable | 88, Unknown.
- **REDCap Guiding Question:** If you have separate fields for T, N, and M stages, how could you potentially create a "calculated field" in REDCap that derives the overall AJCC stage based on these components (if a simple lookup table is available)? (This is an advanced REDCap concept, but good to consider).

**Learning Focus:** Understanding cancer staging (a critical concept), specific diagnostic details, data filtering, and how to represent complex categorical data in REDCap using dropdowns for standardization.

## **Practice Scenario 3: Treatment Information**

**Simulated Researcher Request:** "From the TCGA-THCA data, which patients received radiation therapy? And for those, did they also receive chemotherapy?"

**Files to Use:**

- clinical.tsv: Often has fields indicating whether a patient received specific types of treatment (e.g., radiation_therapy_yes_no, pharmaceutical_therapy_yes_no). It might also have more detailed treatment fields.

### **Practice Steps & Educational Guidance:**

1. **Explore** clinical.tsv**:**
    - Identify columns related to: **Patient ID**, **radiation treatment**, and **chemotherapy/pharmaceutical treatment**. Note how these are coded (Yes/No, True/False, specific drug names, etc.).
    - **Guiding Question:** What are the different ways "Yes/No" or "True/False" can be represented in data, and why is it important to standardize this for analysis?
2. **Filter and Combine Logic:**
    - Select patients who received radiation.
    - From that group, identify who also received chemotherapy.
    - **Guiding Question:** In your chosen software (Excel/Python/R), how would you construct the logical condition to identify patients who received **BOTH** radiation and chemotherapy?

### **REDCap Simulation:**

- You'd likely have a "**Treatment**" form in REDCap.
  - This could be a **repeating form** if patients have multiple treatment courses, or a single form if summarizing primary treatment.
  - Fields might include:
    - **Treatment Type** (dropdown: Surgery, Radiation, Chemotherapy, Targeted Therapy, Immunotherapy, Hormone Therapy).
    - If Radiation: Start Date, End Date, Target Site, Dose.
    - If Chemotherapy: Regimen Name, Start Date, End Date, Number of Cycles.
- How would you handle cases where a patient receives multiple types of treatment concurrently or sequentially? (REDCap's **repeating instruments** or **event grids** can handle this).
- **REDCap Guiding Tip:** The "Repeating Instrument" feature in REDCap is crucial for capturing data that occurs multiple times per patient (e.g., multiple treatment regimens, multiple follow-up visits). "Branching Logic" allows you to show specific fields only if certain conditions are met (e.g., show "Radiation Dose" only if "Treatment Type" is "Radiation").
- **REDCap Guiding Question:** When would you choose a "repeating instrument" over just adding many fields to a single form (e.g., "Radiation Treatment 1 Start Date", "Radiation Treatment 2 Start Date")? What are the advantages of repeating instruments for data analysis?

**Learning Focus:** Understanding treatment variables, combining logical conditions, and thinking about how to capture complex/longitudinal treatment data in REDCap.

## **Practice Scenario 4: Using** pathology_detail.tsv

**Simulated Researcher Request:** "For TCGA-HNSC, I'm interested in patients who had information on lymph node involvement from their pathology reports. Specifically, how many lymph nodes were examined, and how many were positive?"

**Files to Use:**

- pathology_detail.tsv: This file often contains more granular data from pathology reports.
- clinical.tsv: To link back to overall patient data if needed.

### **Practice Steps & Educational Guidance:**

1. **Explore** pathology_detail.tsv**:**
    - Look for columns like: **Patient ID**, number_of_lymphnodes_examined, number_of_lymphnodes_positive_by_he (hematoxylin and eosin stain), or similar.
    - Note that one patient might have multiple pathology entries (e.g., from a biopsy and then a surgical resection). You might need to identify the most relevant one.
    - **Guiding Question:** If a patient has multiple pathology entries for lymph nodes, how would you decide which one to use? Would you take the maximum, the one from the definitive surgical resection, or something else? Why?
2. **Merge and Analyze:**
    - Link this to the patient ID.
    - For patients with this data, what's the average number of nodes examined? Average positive?
    - **Guiding Question:** What would be the statistical distribution for "number of lymph nodes examined" and "number of lymph nodes positive"? Would a simple average be sufficient, or would you want to look at median and range too?

### **REDCap Simulation:**

- You might have a "**Pathology Report Details**" form in REDCap.
  - Fields could include: Specimen Type, Pathology Report Date, Number of Lymph Nodes Examined, Number of Lymph Nodes Positive, details on tumor grade, margins, etc.
  - This form could be repeating if multiple specimens are analyzed.
- **REDCap Guiding Tip:** When designing forms for detailed pathology data, consider the hierarchy. You might have a "Main Pathology" form and then a "Repeating Specimen Details" form nested within it, or a repeating form at the patient level.
- **REDCap Guiding Question:** If you collect number_of_lymphnodes_examined and number_of_lymphnodes_positive, could you use a calculated field in REDCap to determine the percentage of positive lymph nodes? What **validation rule** would you apply to ensure that number_of_lymphnodes_positive is not greater than number_of_lymphnodes_examined?

**Learning Focus:** Delving into more specific data types (pathology), understanding that data can come from different report types, and thinking about granularity.

## **Practice Scenario 5: Risk Factors - Exposure & Family History**

**Simulated Researcher Request:** "Do we have information on smoking history or family history of cancer for the TCGA-THCA patients?"

**Files to Use:**

- exposure.tsv: Often contains information on smoking, alcohol use, environmental exposures.
- family_history.tsv: Information on cancer history in relatives.

### **Practice Steps & Educational Guidance:**

1. **Explore** exposure.tsv**:**
    - Look for smoking-related columns (e.g., cigarettes_per_day, years_smoked, smoking_history). How is 'never smoker' vs 'former smoker' vs 'current smoker' coded?
    - **Guiding Question:** How would you calculate "pack-years" if you have cigarettes_per_day and years_smoked? What are the common classifications for smoking status in epidemiological studies?
2. **Explore** family_history.tsv**:**
    - What fields indicate if there's a family history of cancer? Is the type of cancer in the relative specified?
    - **Guiding Question:** What other details might be important to capture about family history (e.g., relationship to proband, age of diagnosis in relative)?

### **REDCap Simulation:**

- A "**Risk Factors**" or "**Social History**" form in REDCap.
  - Fields for Smoking Status (dropdown), Pack-Years (calculated field if you collect cigarettes_per_day and years_smoked).
  - A "Family History" section, perhaps with repeating fields for each affected relative (Relative, Type of Cancer, Age at Diagnosis).
- **REDCap Guiding Tip:** For "Family History," a **repeating instrument** for "Affected Relatives" is ideal. Within that repeating instrument, you can have fields for relative_type (e.g., Mother, Father, Sibling), cancer_type, age_at_diagnosis_relative, etc.
- **REDCap Guiding Question:** How could you use "**matrix fields**" in REDCap to efficiently capture information about multiple types of exposures (e.g., alcohol, occupational exposures) with similar response options?

**Learning Focus:** Exploring risk factor data, which often requires careful definition of categories.

### **General Tips for Your Practice:**

- **Start Simple:** Don't try to merge all 5 files at once. Work with one or two at a time.
- **Use the Data Dictionary RELIGIOUSLY:** The column names can sometimes be cryptic. The data dictionary is your map. The GDC portal usually has this available for download or online viewing for each project. **Crucial Tip:** Always download and refer to the data dictionary (or "data model") specific to the TCGA project you are working on (e.g., THCA, HNSC). This will define every variable, its type, and its allowed values.
- **Document Your Steps:** Even if it's just a text file, write down what you did, what you found, and any questions you had. This will help you learn and remember.
- **Focus on Understanding, Not Complex Analysis:** The goal now is to get comfortable with the type of data and how it's structured, not to run advanced statistics.
- **Don't Be Afraid of "Messy" Data:** You'll encounter missing values (often coded as \[Not Available\], \[Unknown\], NA, or just blank). Think about how you would handle these if you were preparing data for a researcher or for REDCap entry.
- **Regarding Race and Ethnicity Data (Sensitive Topic):**
  - When dealing with race data, be aware of the categories provided in the TCGA data. TCGA generally uses broad categories.
  - **Concrete Numbers Example (Illustrative - you'll find actual numbers in the data):** In a hypothetical TCGA cohort, you might find that out of 500 patients, 380 (76%) are "White", 50 (10%) are "Black or African American", 20 (4%) are "Asian", and 50 (10%) are "Not Reported" or \[Not Available\].
  - **Educational Guidance:** It's important to understand _how_ race and ethnicity data were collected and categorized, as these classifications can vary across studies and over time. Researchers use this information to investigate disparities in disease incidence, treatment response, and outcomes, but it's critical to interpret such findings with nuance and an understanding of the complex social and biological factors involved. When reporting, always use the exact terminology provided in the dataset and consider providing percentages alongside raw counts for context.
- **Gender vs. Sex (Sensitive Topic):**
  - TCGA data typically records "gender" in a binary fashion (male/female), which often corresponds to biological sex assigned at birth.
  - **Educational Guidance:** In modern data collection, there's a growing distinction between "sex" (biological attributes) and "gender" (social and cultural identity). For TCGA, the term "gender" in the files usually refers to biological sex for research purposes. When designing new REDCap projects, consider if it's appropriate to collect both sex and gender identity, depending on the research question.

This is a significant learning curve, but breaking it down into these kinds of practical scenarios will make it manageable and directly applicable to your new role.