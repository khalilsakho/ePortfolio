# [SAS Project - Impact of Demographic Factors on Earned Income](README.md)


## 📚 Table of Contents

- [🔍 Objective](#-objective)
- [📎 Tools Used](#-tools-used)
- [🔧 Data Manipulation & Cleaning](#-data-manipulation-and-cleaning)
- [📊 Descriptive Statistics and Dataset Merging](#-descriptive-statistics-and-dataset-merging)
- [🧪 Regression Models](#-regression-models)
- [📈 Key Findings](#-key-findings)


## 🔍 Objective

This project explores the empirical relationship between educational attainment and earned income using a detailed dataset of demographic, socioeconomic, and regional variables. The core objective is to determine how different education levels and demographic factors (e.g., age, race, health, citizenship, marital status) influence annual earnings. The analysis uses SAS to generate insights through data visualization, statistical modeling, and regression analysis.

## 📎 Tools Used

- **SAS® OnDemand for Academics** – Primary tool for data import, cleaning, transformation, statistical analysis, and visualization
- **PROC MEANS, PROC FORMAT, PROC SGPLOT, PROC REG** – Key SAS procedures used
- **PDF Report** – Final write-up including figures, interpretations, and model results
  
## 🔧 Data Manipulation and Cleaning

- **Feature Creation**:
  - *Import Data & Create a New Dataset*
```sas
DATA WORK.projdata1;
set e625data.cps_raw_sample;
```
  - *Create Race Variables*
```sas
	IF pehspnon eq 1 THEN hispanic = 1;
	ELSE IF pehspnon eq 2 THEN hispanic = 0;
	
	white = 0;
	black = 0;
	asian = 0;
	other = 0;
	
	IF hispanic EQ 0 AND prdtrace EQ 1 THEN white = 1;
	IF hispanic EQ 0 AND prdtrace EQ 2 THEN black = 1;
	IF hispanic EQ 0 AND prdtrace EQ 3 THEN other = 1;
	IF hispanic EQ 0 AND prdtrace EQ 4 THEN asian = 1;
	IF hispanic EQ 0 AND prdtrace EQ 5 THEN other = 1;
	IF hispanic EQ 0 AND pratrace EQ 6 THEN black = 1;
	IF hispanic EQ 0 AND pratrace EQ 7 THEN other = 1;
	IF hispanic EQ 0 AND pratrace EQ 8 THEN  asian = 1;
	IF hispanic EQ 0 AND prdtrace EQ 9 THEN other = 1;
	IF hispanic EQ 0 AND prdtrace EQ 10 THEN other = 1;
	IF hispanic EQ 0 AND prdtrace EQ 11 THEN asian = 1;
	IF hispanic EQ 0 AND pratrace EQ 12 THEN other = 1;
	IF hispanic EQ 0 AND prdtrace EQ 13 THEN other = 1;
	IF hispanic EQ 0 AND prdtrace EQ 14 THEN other = 1;
	IF hispanic EQ 0 AND prdtrace EQ 15 THEN other = 1;

	* Add labels to variables;
   label White = "White = 1 if the person is white and is not Hispanic, and = 0 otherwise"; 
   label Black = "Black = 1 if the person is black and is not Hispanic, and = 0 otherwise";
   label Asian = "Asian = 1 if the person is Asian and is not Hispanic, and = 0 otherwise";
   label Other = "Other = 1 if the person is of another race and is not Hispanic";
   label Hispanic = "hispanic = 1 if the person is Hispanic regardless of race";
RUN;
```

  - *Create Citizenship & Marital Status Variables*
```sas
   citizen = 0;
   if PRCITSHP ge 1 and PRCITSHP le 4 then citizen = 1;

   * Create marital status variables;
   married = 0;
   widowed = 0;
   divorced = 0;
   separated = 0;
   never_married = 0;

   if A_MARITL in (1, 2, 3) then married = 1;
   else if A_MARITL = 4 then widowed = 1;
   else if A_MARITL = 5 then divorced = 1;
   else if A_MARITL = 6 then separated = 1;
   else if A_MARITL = 7 then never_married = 1;

   * Add labels to variables;
   label citizen = "= 1 if citizen; = 0 otherwise"; 
   label married = "= 1 if married; = 0 otherwise";
   label widowed = "= 1 if widowed; = 0 otherwise";
   label divorced = "= 1 if divorced; = 0 otherwise";
   label separated = "= 1 if separated; = 0 otherwise";
   label never_married = "= 1 if never_married; = 0 otherwise";
RUN;
```
  - *Create Sex Variables*
```sas
	*Used the CPS variable A_SEX to create a variable called female = 1 for females and = 0 for males*;

	* Create female variable*;
	*Female =1 Male =0*;

	Female = 0;
	If A_Sex = 2 Then Female = 1;
	label Female = 'female = 1 for females and = 0 for males';
RUN;
```
  - *Create a variable called earned_income that is equal to PEARNVAL*\
	*Create a variable called Annual_Hours that is equal to usual hours per week X number of weeks worked*\
	*Create a variable called hourly_wage that is equal to Earned_Income/Annual_Hours*;
```sas
	Earned_Income = PEARNVAL;
	Annual_Hours = HRSWK * WKSWORK;
	Hourly_Wage = Earned_Income / Annual_Hours;
	label Earned_Income ='is equal to PEARNVAL;Income Earned in a Year';
	label Annual_Hours ='is equal to usual hours per week X number of weeks worked';
	label Hourly_Wage ='is equal to Earned_Income/Annual_Hours';
RUN;
```

 - *Create Education Variables*
```sas
	educ_lt_hs = 0;
	educ_eq_hs = 0;
	educ_some_college = 0;
	educ_college = 0;
	educ_ma = 0;
	educ_prof_phd = 0;
	
	if A_HGA =< 38 then educ_lt_hs = 1;
	else if A_HGA = 39 then educ_eq_hs = 1;
	else if A_HGA = 40 then educ_some_college = 1;
	else if A_HGA in (41, 42, 43) then educ_college = 1;
	else if A_HGA = 44 then educ_ma = 1;
	else if A_HGA in (45, 46) then educ_prof_phd = 1;
   

   * Add labels to variables;
   label educ_lt_hs = "= 1 if education less than a high school degree; = 0 otherwise"; 
   label educ_eq_hs = "= 1 if education= high school degree; = 0 otherwise";
   label educ_some_college = "= 1 if if education beyond high school but less than bachelors degree; = 0 otherwise";
   label educ_college = "= 1 if college graduate; = 0 otherwise";
   label educ_ma = "= 1 if if masters degree; = 0 otherwise";
   label educ_prof_phd = "= 1 if if professional or doctoral degree; = 0 otherwise";

	*Categorize New Education Variables*;
	
	educ_cat = 0;
	IF educ_lt_hs = 1 THEN educ_cat = 1;
	IF educ_eq_hs = 1 THEN educ_cat = 2;
	IF educ_some_college = 1 THEN educ_cat = 3;
	IF educ_college = 1 THEN educ_cat = 4;
	IF educ_ma = 1 THEN educ_cat = 5;
	IF educ_prof_phd = 1 THEN educ_cat = 6;
RUN;
```

  - *Create A Variable Called poor_health which = 1 if health status is fair or poor and is = 0 otherwise*
```sas
	poor_health = 0;
	IF HEA = 4 THEN poor_health = 1;
	IF HEA = 5 THEN poor_health = 1;
	label poor_health ='= 1 if health status is fair or poor and is = 0 otherwise';
		
		
	miss_educ = 0;
	IF AXHGA ne 0 THEN miss_educ =1;
	LABEL miss_educ = "=1 if AXHGA is not = 0; 0 otherwise";
	
	miss_earn = 0;
	IF I_HRSWK ne 0 OR I_WKSWK ne 0 THEN miss_earn =1;
	LABEL miss_earn = "=1 if missing earnings information."
	
	miss_demo = 0;
	IF  AXAGE ne 0 OR
		AXHGA ne 0 OR
		I_HEA ne 0 OR
		PXHSPNON ne 0 OR
		PXMARITL not in (-1,0) OR
		PXRACE1 ne 0 THEN miss_demo = 1;
		LABEL miss_demo = "=1 if missing demographic information";

	miss_demo_earn = 0;
	IF miss_demo eq 1 OR miss_earn eq 1 THEN miss_demo_earn = 1;
	LABEL miss_demo_earn = "=1 if miss_earn or miss_demo =1";
RUN;
```
## 📊 Descriptive Statistics and Dataset Merging

Once the data was cleaned and transformed, a series of SAS procedures were used to examine variable distributions, join datasets, and prepare a finalized dataset for regression analysis.

---

### 📑 Summary Statistics

- **`PROC MEANS`** was used to generate descriptive statistics for:
  - The cleaned main dataset (`projdata1`)
  - The household-level file (`cps_hh_file`)
  - The merged dataset containing household and regional information (`TEMP`)
  - The final project dataset (`projdata3`)
  - The final analysis-ready dataset (`e625proj.analysis_data`)

  These procedures calculated:
  - Count (`N`)
  - Number of missing values (`NMISS`)
  - Mean, minimum, and maximum values
  - Rounded to 3 decimal places using `MAXDEC=3`

```sas
PROC MEANS DATA=WORK.projdata1;
TITLE "DESCRIPTIVE STATISTICS FOR VARIABLES WITHIN WORK.projdata1";
RUN;

PROC MEANS DATA=e625data.cps_hh_file n nmiss mean min max maxdec=3;
TITLE "DESCRIPTIVE STATISTICS FOR VARIABLES WITHIN E625DATA.CPS_HH_FILE";

PROC MEANS DATA=TEMP;
TITLE "DESCRIPTIVE STATISTICS FOR VARIABLES WITHIN TEMP";

PROC MEANS DATA= work.projdata3;
TITLE "DESCRIPTIVE STATISTICS FOR VARIABLES WITHIN work.projdata3";
RUN;
```

---

### 🧩 Dataset Merging and Enhancements

- **Sorted and merged multiple datasets** using common IDs (`hhid`, `PERIDNUM`) to enrich the analysis dataset:
  - `hhfile` and `unit6` merged by `hhid` to create `TEMP`, which included geographic regional identifiers.
  - Regions (`south`, `northeast`, `midwest`, `west`) were derived from the `GEREG` variable.
  - Median earned income was calculated at the **state level** (`gestfips`) using `PROC MEANS`, and merged into the dataset as `median_income`.
```sas
PROC SORT DATA=e625data.cps_hh_file out=hhfile; 
BY hhid; 

PROC SORT DATA=e625u6.unit6 out=unit6; 
BY hhid; 
RUN;

DATA temp;
MERGE hhfile unit6;
BY hhid;

	south = 0;
	northeast = 0; 
	midwest = 0; 
	west = 0;
	
	IF GEREG = 1 THEN northeast = 1;
	IF GEREG = 2 THEN midwest = 1;
	IF GEREG = 3 THEN south = 1;
	IF GEREG = 4 THEN west = 1;

	LABEL south = 'South Region';
	LABEL northeast = 'Northeast Region'; 
	LABEL midwest = 'Midwest Region'; 
	LABEL west = 'West Region';

PROC SORT DATA= temp;
BY gestfips;

DATA WORK.projdata2;
MERGE temp medianinc;
BY gestfips;
LABEL median_income = "State Median Income";

PROC MEANS DATA = temp NOPRINT;
BY gestfips;
OUTPUT OUT = medianinc MEDIAN(earned_income) = median_income;
TITLE "DESCRIPTIVE STATISTICS FOR VARIABLES WITHIN TEMP by gestfips";
RUN;
	
PROC SORT DATA=work.projdata2 out=projd2; 
BY PERIDNUM; 
  
PROC SORT DATA=e625data.cps_additional_variables out=add_vars; 
BY PERIDNUM;
  
PROC SORT DATA=work.projdata1 out=projd1; 
BY PERIDNUM;
RUN;

DATA WORK.projdata3;
MERGE projd1 projd2 add_vars;
BY PERIDNUM;

RUN; 
```
---

### 🧮 Final Dataset Construction

- Additional variables from `cps_additional_variables` were merged with individual-level data (`projdata1`) and household-level data (`projdata2`) to create a consolidated dataset: `projdata3`.

- **Final analysis dataset**:
  - Saved as `e625proj.analysis_data`
  - Included only key variables necessary for regression and analysis:
    - Income and education dummies
    - Demographics (gender, race, age)
    - Health and marital status
    - Citizenship and region indicators
    - Data quality flags (`miss_demo`, `miss_earn`, `miss_demo_earn`)
    - New features (`educyrs12`, `median_income`)
```sas
DATA e625proj.analysis_data;
SET work.projdata3;
KEEP earned_income educ_eq_hs educ_some_college educ_college educ_ma educ_prof_phd 
	age18 poor_health female black hispanic asian other citizen married 
	divorced separated widowed median_income northeast midwest west educyrs educ_cat miss_demo educyrs12 miss_earn miss_demo_earn;
RUN;
```

- **Final inspection**:
  - Used `PROC CONTENTS` and `PROC MEANS` to ensure the dataset was correctly structured and statistically sound before modeling.
```sas
PROC MEANS DATA= e625proj.analysis_data;
TITLE "DESCRIPTIVE STATISTICS FOR FINAL VARIABLES";
PROC CONTENTS DATA= e625proj.analysis_data;
RUN;
```
---

This process ensured the final dataset was comprehensive, statistically validated, and ready for regression analysis.

## 🧪 Regression Models
This project estimated multiple regression models to analyze how education and demographic factors affect earned income. The regressions were carefully designed to test for consistency between respondents with and without imputed or allocated data.

---

### 📊 Cross-Tabulation of Missing Data

Before modeling, a frequency cross-tabulation (`PROC FREQ`) was conducted using:

```sas
PROC FORMAT;
VALUE missinfo
	0 = "Not Allocated/Imputed"
	1 = "Allocated/Imputed";
PROC FREQ DATA=e625proj.analysis_data NOPRINT;
TABLE miss_demo*miss_earn / NOCOL NOFREQ NOPERCENT OUT=MissingDataCrossTabulations;
WHERE miss_demo = 1 AND miss_earn = 1;
FORMAT miss_demo missinfo. miss_earn missinfo.;  

PROC PRINT DATA=MissingDataCrossTabulations;
TITLE "Table of Cross Tabulations of miss_demo and miss_earn";
RUN;
```
This allowed us to assess how many observations were affected by missing demographic and earnings data. Only individuals without any imputed data (miss_demo = 0, miss_earn = 0) were included in the primary regression to ensure robust and unbiased estimates.

### 📈 Primary Regression Model (Without Imputed Data)
```sas
PROC REG DATA= e625proj.analysis_data PLOTS=NONE;
TITLE "Multiple Regression Without Imputed/Allocated Demographic or Earnings Data";
WHERE miss_demo = 0 & miss_earn = 0;
MODEL earned_income = educ_eq_hs educ_some_college educ_college educ_ma educ_prof_phd 
		  age18 poor_health female black hispanic asian other citizen married divorced 
		  separated widowed median_income northeast midwest west;
RUN;
```
This regression estimates the effect of education (as a series of dummy variables) and other demographic controls on earned income. 

### 🧪 Joint Hypothesis Test (Education Variables)
To determine whether all education dummies jointly contribute to the model, a TEST statement was used:
```sas
PROC REG DATA= e625proj.analysis_data PLOTS=NONE;
TITLE "Multiple Regression Without Imputed/Allocated Demographic or Earnings Data (T)";
WHERE miss_demo = 0 & miss_earn = 0;
MODEL earned_income = educ_eq_hs educ_some_college educ_college educ_ma educ_prof_phd 
		  age18 poor_health female black hispanic asian other citizen married divorced 
		  separated widowed median_income northeast midwest west;
TEST educ_eq_hs = 0, educ_some_college = 0, educ_college = 0, educ_ma = 0, educ_prof_phd = 0;
RUN;
```
This F-test evaluates whether the education variables collectively improve the model's explanatory power. The result was statistically significant (p < 0.0001), confirming that education is a key predictor of income.

### 🧪 Comparison Model (With Imputed Data)
For robustness, the same regression model was re-estimated using only respondents with imputed demographic and earnings data (miss_demo = 1 & miss_earn = 1). This helps assess how missing data treatment may affect conclusions.
```sas
PROC REG DATA= e625proj.analysis_data PLOTS=NONE;
TITLE "Multiple Regression on Imputed/Allocated Demographic or Earnings Data";
WHERE miss_demo = 1 & miss_earn = 1;
MODEL earned_income = educ_eq_hs educ_some_college educ_college educ_ma educ_prof_phd 
		  age18 poor_health female black hispanic asian other citizen married divorced 
		  separated widowed median_income northeast midwest west;
RUN;
```
The direction and magnitude of most coefficients remained similar, but there were small variations in effect sizes—especially for variables like gender, marital status, and education. These differences were documented in the final report.

## 📈 Key Findings

The results of this analysis reveal several important insights into the relationship between education, demographics, and earned income:

---

### 🎓 Education and Income

- **Positive Impact of Education**: Each higher level of education is associated with a statistically significant increase in earned income.
  - High School Diploma → +$7,888
  - Some College → +$12,928
  - Bachelor's Degree → +$29,345
  - Master’s Degree → +$47,272
  - Professional/PhD → +$69,212

- **Joint Significance**: A hypothesis test confirmed that all education variables collectively contribute significantly to income prediction (p < 0.0001).

---

### 👥 Demographic Effects

- **Gender Gap**: Females earn approximately **$25,129 less** than males, controlling for other factors.
- **Race and Ethnicity**:
  - Black individuals earn ~$4,994 less than non-Black, non-Hispanic individuals.
  - Hispanic individuals earn ~$7,014 less on average.
  - Asian and Other race categories showed mixed or statistically insignificant results.
- **Marital Status**:
  - Being married is associated with an income increase of ~$7,542.
  - Divorced and separated individuals also earn more than never-married individuals, though the effect is smaller.
- **Citizenship**: Citizens earn ~$5,150 more than non-citizens on average.

---

### 💡 Other Factors

- **Age**: Each year above age 18 is associated with a ~$506 increase in earned income.
- **Health**: Individuals in fair or poor health earn ~$10,831 less than healthier counterparts.
- **Geography**:
  - Living in the **West** region is associated with a ~$3,891 increase in income.
  - No statistically significant income differences were found for the Northeast or Midwest regions.

---

### 🧪 Missing Data Sensitivity

- Regression results were robust when comparing models **with** and **without** imputed or allocated data.
- Most key variables remained significant across both models, though effect sizes for some (e.g., gender, health, marital status) varied slightly.

---

### 📊 Model Performance

- **R² = 0.3081**: The final regression model explains about 30.81% of the variance in earned income.
- While this is a moderate level of explanatory power, it strongly supports the role of **education** and **demographics** as key determinants of income.

---

### 📌 Conclusion

This analysis confirms the powerful economic impact of educational attainment and highlights how other demographic and regional factors contribute to income inequality in the U.S. labor market. These insights can inform policy discussions around education access, wage equity, and economic opportunity.
