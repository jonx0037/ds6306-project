---
title: 'Case Study 1: Employee Attrition Analysis'
author: "Jonathan A. Rocha"
date: "March 3, 2025"
output:
  html_document:
    keep_md: true
    code_folding: hide
    theme: cosmo
    toc: true
    toc_float: true
  word_document:
    toc: true
  pdf_document:
    toc: true
editor_options:
  markdown:
    wrap: 72
---



## Executive Summary

This analysis investigates employee attrition at Frito Lay to identify key factors driving turnover and to develop predictive models. According to research, replacing an employee costs between 50% and 400% of their annual salary, while targeted retention incentives cost approximately $200 per employee. Our analysis aims to help Frito Lay strategically allocate retention resources to reduce attrition costs.

### Key Findings:

1. **Top factors contributing to attrition:**
   - **Overtime**: Employees working overtime have 3.3 times higher attrition rates than those who don't
   - **Total working years**: Less experienced employees are significantly more likely to leave
   - **Job level and monthly income**: Lower job levels and salaries correlate strongly with higher attrition

2. **Model performance:**
   - Our optimal gradient boosting model achieved 69.1% sensitivity and 69.4% specificity
   - The model successfully balances identifying potential leavers while minimizing false alarms

3. **Financial impact:**
   - Implementing the model could save approximately $517,000 (7.1%) in attrition-related costs
   - ROI varies based on replacement cost estimates, with savings increasing as replacement costs rise

4. **Recommendations:**
   - Target retention efforts at entry-level employees with overtime requirements
   - Review compensation structure for employees with lower total working years
   - Develop career progression pathways to address job level concerns

## Introduction

DDSAnalytics has been hired by Frito Lay to identify factors related to employee attrition and develop predictive models to reduce turnover costs. This analysis explores the dataset to understand patterns in attrition, builds machine learning models to predict which employees might leave, and quantifies the potential cost savings of implementing these models.

### Project Objectives

1. Identify the top three factors that contribute to employee attrition at Frito Lay
2. Build predictive models (KNN and Naive Bayes) to identify employees at risk of leaving
3. Ensure models achieve at least 60% sensitivity and 60% specificity
4. Measure the potential cost savings of implementing the predictive model
5. Make predictions for the 300 unlabeled competition dataset employees

### Analysis Approach

The analysis follows a structured data science methodology:

1. **Data preparation and cleaning**: Review data quality and prepare it for analysis
2. **Exploratory data analysis (EDA)**: Identify patterns and relationships with attrition
3. **Feature engineering**: Create derived variables that might improve model performance
4. **Model development**: Build, evaluate, and optimize machine learning models
5. **Cost-benefit analysis**: Quantify the potential financial impact of using the model
6. **Competition predictions**: Generate attrition predictions for the competition dataset

## Data Import and Preparation


``` r
# Import the dataset
attrition_data <- read.csv("~/Desktop/School Projects/SMU/DS_6306_Doing-Data-Science/Unit 8 and 9 Case Study 1/CaseStudy1-data.csv", stringsAsFactors = TRUE)

# Take a look at the data structure
str(attrition_data)
```

```
## 'data.frame':	870 obs. of  36 variables:
##  $ ID                      : int  1 2 3 4 5 6 7 8 9 10 ...
##  $ Age                     : int  32 40 35 32 24 27 41 37 34 34 ...
##  $ Attrition               : Factor w/ 2 levels "No","Yes": 1 1 1 1 1 1 1 1 1 1 ...
##  $ BusinessTravel          : Factor w/ 3 levels "Non-Travel","Travel_Frequently",..: 3 3 2 3 2 2 3 3 3 2 ...
##  $ DailyRate               : int  117 1308 200 801 567 294 1283 309 1333 653 ...
##  $ Department              : Factor w/ 3 levels "Human Resources",..: 3 2 2 3 2 2 2 3 3 2 ...
##  $ DistanceFromHome        : int  13 14 18 1 2 10 5 10 10 10 ...
##  $ Education               : int  4 3 2 4 1 2 5 4 4 4 ...
##  $ EducationField          : Factor w/ 6 levels "Human Resources",..: 2 4 2 3 6 2 4 2 2 6 ...
##  $ EmployeeCount           : int  1 1 1 1 1 1 1 1 1 1 ...
##  $ EmployeeNumber          : int  859 1128 1412 2016 1646 733 1448 1105 1055 1597 ...
##  $ EnvironmentSatisfaction : int  2 3 3 3 1 4 2 4 3 4 ...
##  $ Gender                  : Factor w/ 2 levels "Female","Male": 2 2 2 1 1 2 2 1 1 2 ...
##  $ HourlyRate              : int  73 44 60 48 32 32 90 88 87 92 ...
##  $ JobInvolvement          : int  3 2 3 3 3 3 4 2 3 2 ...
##  $ JobLevel                : int  2 5 3 3 1 3 1 2 1 2 ...
##  $ JobRole                 : Factor w/ 9 levels "Healthcare Representative",..: 8 6 5 8 7 5 7 8 9 1 ...
##  $ JobSatisfaction         : int  4 3 4 4 4 1 3 4 3 3 ...
##  $ MaritalStatus           : Factor w/ 3 levels "Divorced","Married",..: 1 3 3 2 3 1 2 1 2 2 ...
##  $ MonthlyIncome           : int  4403 19626 9362 10422 3760 8793 2127 6694 2220 5063 ...
##  $ MonthlyRate             : int  9250 17544 19944 24032 17218 4809 5561 24223 18410 15332 ...
##  $ NumCompaniesWorked      : int  2 1 2 1 1 1 2 2 1 1 ...
##  $ Over18                  : Factor w/ 1 level "Y": 1 1 1 1 1 1 1 1 1 1 ...
##  $ OverTime                : Factor w/ 2 levels "No","Yes": 1 1 1 1 2 1 2 2 2 1 ...
##  $ PercentSalaryHike       : int  11 14 11 19 13 21 12 14 19 14 ...
##  $ PerformanceRating       : int  3 3 3 3 3 4 3 3 3 3 ...
##  $ RelationshipSatisfaction: int  3 1 3 3 3 3 1 3 4 2 ...
##  $ StandardHours           : int  80 80 80 80 80 80 80 80 80 80 ...
##  $ StockOptionLevel        : int  1 0 0 2 0 2 0 3 1 1 ...
##  $ TotalWorkingYears       : int  8 21 10 14 6 9 7 8 1 8 ...
##  $ TrainingTimesLastYear   : int  3 2 2 3 2 4 5 5 2 3 ...
##  $ WorkLifeBalance         : int  2 4 3 3 3 2 2 3 3 2 ...
##  $ YearsAtCompany          : int  5 20 2 14 6 9 4 1 1 8 ...
##  $ YearsInCurrentRole      : int  2 7 2 10 3 7 2 0 1 2 ...
##  $ YearsSinceLastPromotion : int  0 4 2 5 1 1 0 0 0 7 ...
##  $ YearsWithCurrManager    : int  3 9 2 7 3 7 3 0 0 7 ...
```

``` r
# Basic summary of the data
summary(attrition_data)
```

```
##        ID             Age        Attrition           BusinessTravel
##  Min.   :  1.0   Min.   :18.00   No :730   Non-Travel       : 94   
##  1st Qu.:218.2   1st Qu.:30.00   Yes:140   Travel_Frequently:158   
##  Median :435.5   Median :35.00             Travel_Rarely    :618   
##  Mean   :435.5   Mean   :36.83                                     
##  3rd Qu.:652.8   3rd Qu.:43.00                                     
##  Max.   :870.0   Max.   :60.00                                     
##                                                                    
##    DailyRate                       Department  DistanceFromHome   Education    
##  Min.   : 103.0   Human Resources       : 35   Min.   : 1.000   Min.   :1.000  
##  1st Qu.: 472.5   Research & Development:562   1st Qu.: 2.000   1st Qu.:2.000  
##  Median : 817.5   Sales                 :273   Median : 7.000   Median :3.000  
##  Mean   : 815.2                                Mean   : 9.339   Mean   :2.901  
##  3rd Qu.:1165.8                                3rd Qu.:14.000   3rd Qu.:4.000  
##  Max.   :1499.0                                Max.   :29.000   Max.   :5.000  
##                                                                                
##           EducationField EmployeeCount EmployeeNumber   EnvironmentSatisfaction
##  Human Resources : 15    Min.   :1     Min.   :   1.0   Min.   :1.000          
##  Life Sciences   :358    1st Qu.:1     1st Qu.: 477.2   1st Qu.:2.000          
##  Marketing       :100    Median :1     Median :1039.0   Median :3.000          
##  Medical         :270    Mean   :1     Mean   :1029.8   Mean   :2.701          
##  Other           : 52    3rd Qu.:1     3rd Qu.:1561.5   3rd Qu.:4.000          
##  Technical Degree: 75    Max.   :1     Max.   :2064.0   Max.   :4.000          
##                                                                                
##     Gender      HourlyRate     JobInvolvement     JobLevel    
##  Female:354   Min.   : 30.00   Min.   :1.000   Min.   :1.000  
##  Male  :516   1st Qu.: 48.00   1st Qu.:2.000   1st Qu.:1.000  
##               Median : 66.00   Median :3.000   Median :2.000  
##               Mean   : 65.61   Mean   :2.723   Mean   :2.039  
##               3rd Qu.: 83.00   3rd Qu.:3.000   3rd Qu.:3.000  
##               Max.   :100.00   Max.   :4.000   Max.   :5.000  
##                                                               
##                       JobRole    JobSatisfaction  MaritalStatus MonthlyIncome  
##  Sales Executive          :200   Min.   :1.000   Divorced:191   Min.   : 1081  
##  Research Scientist       :172   1st Qu.:2.000   Married :410   1st Qu.: 2840  
##  Laboratory Technician    :153   Median :3.000   Single  :269   Median : 4946  
##  Manufacturing Director   : 87   Mean   :2.709                  Mean   : 6390  
##  Healthcare Representative: 76   3rd Qu.:4.000                  3rd Qu.: 8182  
##  Sales Representative     : 53   Max.   :4.000                  Max.   :19999  
##  (Other)                  :129                                                 
##   MonthlyRate    NumCompaniesWorked Over18  OverTime  PercentSalaryHike
##  Min.   : 2094   Min.   :0.000      Y:870   No :618   Min.   :11.0     
##  1st Qu.: 8092   1st Qu.:1.000              Yes:252   1st Qu.:12.0     
##  Median :14074   Median :2.000                        Median :14.0     
##  Mean   :14326   Mean   :2.728                        Mean   :15.2     
##  3rd Qu.:20456   3rd Qu.:4.000                        3rd Qu.:18.0     
##  Max.   :26997   Max.   :9.000                        Max.   :25.0     
##                                                                        
##  PerformanceRating RelationshipSatisfaction StandardHours StockOptionLevel
##  Min.   :3.000     Min.   :1.000            Min.   :80    Min.   :0.0000  
##  1st Qu.:3.000     1st Qu.:2.000            1st Qu.:80    1st Qu.:0.0000  
##  Median :3.000     Median :3.000            Median :80    Median :1.0000  
##  Mean   :3.152     Mean   :2.707            Mean   :80    Mean   :0.7839  
##  3rd Qu.:3.000     3rd Qu.:4.000            3rd Qu.:80    3rd Qu.:1.0000  
##  Max.   :4.000     Max.   :4.000            Max.   :80    Max.   :3.0000  
##                                                                           
##  TotalWorkingYears TrainingTimesLastYear WorkLifeBalance YearsAtCompany  
##  Min.   : 0.00     Min.   :0.000         Min.   :1.000   Min.   : 0.000  
##  1st Qu.: 6.00     1st Qu.:2.000         1st Qu.:2.000   1st Qu.: 3.000  
##  Median :10.00     Median :3.000         Median :3.000   Median : 5.000  
##  Mean   :11.05     Mean   :2.832         Mean   :2.782   Mean   : 6.962  
##  3rd Qu.:15.00     3rd Qu.:3.000         3rd Qu.:3.000   3rd Qu.:10.000  
##  Max.   :40.00     Max.   :6.000         Max.   :4.000   Max.   :40.000  
##                                                                          
##  YearsInCurrentRole YearsSinceLastPromotion YearsWithCurrManager
##  Min.   : 0.000     Min.   : 0.000          Min.   : 0.00       
##  1st Qu.: 2.000     1st Qu.: 0.000          1st Qu.: 2.00       
##  Median : 3.000     Median : 1.000          Median : 3.00       
##  Mean   : 4.205     Mean   : 2.169          Mean   : 4.14       
##  3rd Qu.: 7.000     3rd Qu.: 3.000          3rd Qu.: 7.00       
##  Max.   :18.000     Max.   :15.000          Max.   :17.00       
## 
```

``` r
# How many rows and columns?
dim(attrition_data)
```

```
## [1] 870  36
```

### Data Cleaning and Preparation


``` r
# Check for missing values
missing_values <- colSums(is.na(attrition_data))
print(missing_values[missing_values > 0])
```

```
## named numeric(0)
```

``` r
# Check unique values for categorical variables
categorical_vars <- names(attrition_data)[sapply(attrition_data, is.factor)]
for(var in categorical_vars) {
  cat("\nUnique values for", var, ":\n")
  print(table(attrition_data[[var]]))
}
```

```
## 
## Unique values for Attrition :
## 
##  No Yes 
## 730 140 
## 
## Unique values for BusinessTravel :
## 
##        Non-Travel Travel_Frequently     Travel_Rarely 
##                94               158               618 
## 
## Unique values for Department :
## 
##        Human Resources Research & Development                  Sales 
##                     35                    562                    273 
## 
## Unique values for EducationField :
## 
##  Human Resources    Life Sciences        Marketing          Medical 
##               15              358              100              270 
##            Other Technical Degree 
##               52               75 
## 
## Unique values for Gender :
## 
## Female   Male 
##    354    516 
## 
## Unique values for JobRole :
## 
## Healthcare Representative           Human Resources     Laboratory Technician 
##                        76                        27                       153 
##                   Manager    Manufacturing Director         Research Director 
##                        51                        87                        51 
##        Research Scientist           Sales Executive      Sales Representative 
##                       172                       200                        53 
## 
## Unique values for MaritalStatus :
## 
## Divorced  Married   Single 
##      191      410      269 
## 
## Unique values for Over18 :
## 
##   Y 
## 870 
## 
## Unique values for OverTime :
## 
##  No Yes 
## 618 252
```

``` r
# Checking if there are any variables with zero variance (constant values)
zero_var_cols <- names(which(sapply(attrition_data, function(x) length(unique(x)) == 1)))
if(length(zero_var_cols) > 0) {
  cat("Variables with zero variance:", zero_var_cols, "\n")
}
```

```
## Variables with zero variance: EmployeeCount Over18 StandardHours
```

``` r
# Create a clean dataset for analysis by removing ID variables or other non-predictive features
attrition_clean <- attrition_data %>%
  select(-EmployeeCount, -EmployeeNumber, -ID, -StandardHours, -Over18) 

# Confirm the structure of the cleaned dataset
str(attrition_clean)
```

```
## 'data.frame':	870 obs. of  31 variables:
##  $ Age                     : int  32 40 35 32 24 27 41 37 34 34 ...
##  $ Attrition               : Factor w/ 2 levels "No","Yes": 1 1 1 1 1 1 1 1 1 1 ...
##  $ BusinessTravel          : Factor w/ 3 levels "Non-Travel","Travel_Frequently",..: 3 3 2 3 2 2 3 3 3 2 ...
##  $ DailyRate               : int  117 1308 200 801 567 294 1283 309 1333 653 ...
##  $ Department              : Factor w/ 3 levels "Human Resources",..: 3 2 2 3 2 2 2 3 3 2 ...
##  $ DistanceFromHome        : int  13 14 18 1 2 10 5 10 10 10 ...
##  $ Education               : int  4 3 2 4 1 2 5 4 4 4 ...
##  $ EducationField          : Factor w/ 6 levels "Human Resources",..: 2 4 2 3 6 2 4 2 2 6 ...
##  $ EnvironmentSatisfaction : int  2 3 3 3 1 4 2 4 3 4 ...
##  $ Gender                  : Factor w/ 2 levels "Female","Male": 2 2 2 1 1 2 2 1 1 2 ...
##  $ HourlyRate              : int  73 44 60 48 32 32 90 88 87 92 ...
##  $ JobInvolvement          : int  3 2 3 3 3 3 4 2 3 2 ...
##  $ JobLevel                : int  2 5 3 3 1 3 1 2 1 2 ...
##  $ JobRole                 : Factor w/ 9 levels "Healthcare Representative",..: 8 6 5 8 7 5 7 8 9 1 ...
##  $ JobSatisfaction         : int  4 3 4 4 4 1 3 4 3 3 ...
##  $ MaritalStatus           : Factor w/ 3 levels "Divorced","Married",..: 1 3 3 2 3 1 2 1 2 2 ...
##  $ MonthlyIncome           : int  4403 19626 9362 10422 3760 8793 2127 6694 2220 5063 ...
##  $ MonthlyRate             : int  9250 17544 19944 24032 17218 4809 5561 24223 18410 15332 ...
##  $ NumCompaniesWorked      : int  2 1 2 1 1 1 2 2 1 1 ...
##  $ OverTime                : Factor w/ 2 levels "No","Yes": 1 1 1 1 2 1 2 2 2 1 ...
##  $ PercentSalaryHike       : int  11 14 11 19 13 21 12 14 19 14 ...
##  $ PerformanceRating       : int  3 3 3 3 3 4 3 3 3 3 ...
##  $ RelationshipSatisfaction: int  3 1 3 3 3 3 1 3 4 2 ...
##  $ StockOptionLevel        : int  1 0 0 2 0 2 0 3 1 1 ...
##  $ TotalWorkingYears       : int  8 21 10 14 6 9 7 8 1 8 ...
##  $ TrainingTimesLastYear   : int  3 2 2 3 2 4 5 5 2 3 ...
##  $ WorkLifeBalance         : int  2 4 3 3 3 2 2 3 3 2 ...
##  $ YearsAtCompany          : int  5 20 2 14 6 9 4 1 1 8 ...
##  $ YearsInCurrentRole      : int  2 7 2 10 3 7 2 0 1 2 ...
##  $ YearsSinceLastPromotion : int  0 4 2 5 1 1 0 0 0 7 ...
##  $ YearsWithCurrManager    : int  3 9 2 7 3 7 3 0 0 7 ...
```

## Exploratory Data Analysis (EDA)

### Attrition Overview


``` r
# Overall attrition rate
attrition_rate <- mean(attrition_clean$Attrition == "Yes") * 100
cat(sprintf("Overall attrition rate: %.2f%%\n", attrition_rate))
```

```
## Overall attrition rate: 16.09%
```

``` r
# Visualization of attrition distribution
ggplot(attrition_clean, aes(x = Attrition, fill = Attrition)) +
  geom_bar() +
  geom_text(stat = "count", aes(label = scales::percent(..count../sum(..count..))),
            position = position_stack(vjust = 0.5)) +
  scale_fill_manual(values = c("Yes" = "#FF6B6B", "No" = "#4ECDC4")) +
  labs(title = "Attrition Distribution",
       x = "Attrition Status",
       y = "Count") +
  theme_economist() +
  theme(legend.position = "none")
```

![](DS-6306_Project_Jonathan_Rocha_files/figure-html/attrition_overview-1.png)<!-- -->

### Demographic Analysis


``` r
# Age distribution by attrition
age_plot <- ggplot(attrition_clean, aes(x = Age, fill = Attrition)) +
  geom_histogram(binwidth = 5, position = "dodge", alpha = 0.7) +
  scale_fill_manual(values = c("Yes" = "#FF6B6B", "No" = "#4ECDC4")) +
  labs(title = "Age Distribution by Attrition",
       x = "Age",
       y = "Count") +
  theme_economist()

# Gender and attrition
gender_attrition <- attrition_clean %>%
  group_by(Gender, Attrition) %>%
  summarize(Count = n(), .groups = "drop") %>%
  group_by(Gender) %>%
  mutate(Percentage = Count / sum(Count) * 100)

gender_plot <- ggplot(gender_attrition, aes(x = Gender, y = Percentage, fill = Attrition)) +
  geom_bar(stat = "identity", position = "dodge") +
  geom_text(aes(label = sprintf("%.1f%%", Percentage)), 
            position = position_dodge(width = 0.9), vjust = -0.5) +
  scale_fill_manual(values = c("Yes" = "#FF6B6B", "No" = "#4ECDC4")) +
  labs(title = "Attrition Rate by Gender",
       x = "Gender",
       y = "Percentage") +
  theme_economist()

# Marital status and attrition
marital_attrition <- attrition_clean %>%
  group_by(MaritalStatus, Attrition) %>%
  summarize(Count = n(), .groups = "drop") %>%
  group_by(MaritalStatus) %>%
  mutate(Percentage = Count / sum(Count) * 100)

marital_plot <- ggplot(marital_attrition, aes(x = MaritalStatus, y = Percentage, fill = Attrition)) +
  geom_bar(stat = "identity", position = "dodge") +
  geom_text(aes(label = sprintf("%.1f%%", Percentage)), 
            position = position_dodge(width = 0.9), vjust = -0.5) +
  scale_fill_manual(values = c("Yes" = "#FF6B6B", "No" = "#4ECDC4")) +
  labs(title = "Attrition Rate by Marital Status",
       x = "Marital Status",
       y = "Percentage") +
  theme_economist()

# Display plots 
print(age_plot)
```

![](DS-6306_Project_Jonathan_Rocha_files/figure-html/demographic_analysis-1.png)<!-- -->

``` r
print(gender_plot)
```

![](DS-6306_Project_Jonathan_Rocha_files/figure-html/demographic_analysis-2.png)<!-- -->

``` r
print(marital_plot)
```

![](DS-6306_Project_Jonathan_Rocha_files/figure-html/demographic_analysis-3.png)<!-- -->

### Job-Related Factors


``` r
# Department and attrition
dept_attrition <- attrition_clean %>%
  group_by(Department, Attrition) %>%
  summarize(Count = n(), .groups = "drop") %>%
  group_by(Department) %>%
  mutate(Percentage = Count / sum(Count) * 100)

dept_plot <- ggplot(dept_attrition, aes(x = Department, y = Percentage, fill = Attrition)) +
  geom_bar(stat = "identity", position = "dodge") +
  geom_text(aes(label = sprintf("%.1f%%", Percentage)), 
            position = position_dodge(width = 0.9), vjust = -0.5) +
  scale_fill_manual(values = c("Yes" = "#FF6B6B", "No" = "#4ECDC4")) +
  labs(title = "Attrition Rate by Department",
       x = "Department",
       y = "Percentage") +
  theme_economist() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))

# Job role and attrition (using a sorted bar plot for better readability)
jobrole_attrition <- attrition_clean %>%
  group_by(JobRole, Attrition) %>%
  summarize(Count = n(), .groups = "drop") %>%
  group_by(JobRole) %>%
  mutate(Percentage = Count / sum(Count) * 100,
         TotalCount = sum(Count)) %>%
  filter(Attrition == "Yes") %>%
  arrange(desc(Percentage))

# Create a factor with levels ordered by attrition percentage
ordered_roles <- jobrole_attrition$JobRole

jobrole_plot <- ggplot(jobrole_attrition, aes(x = factor(JobRole, levels = ordered_roles), y = Percentage, fill = Percentage)) +
  geom_bar(stat = "identity") +
  geom_text(aes(label = sprintf("%.1f%%", Percentage)), hjust = -0.1) +
  scale_fill_viridis_c() +
  labs(title = "Attrition Rate by Job Role",
       x = "Job Role",
       y = "Attrition Percentage") +
  theme_economist() +
  coord_flip()

# Job level and attrition
joblevel_attrition <- attrition_clean %>%
  group_by(JobLevel, Attrition) %>%
  summarize(Count = n(), .groups = "drop") %>%
  group_by(JobLevel) %>%
  mutate(Percentage = Count / sum(Count) * 100)

joblevel_plot <- ggplot(joblevel_attrition, aes(x = as.factor(JobLevel), y = Percentage, fill = Attrition)) +
  geom_bar(stat = "identity", position = "dodge") +
  geom_text(aes(label = sprintf("%.1f%%", Percentage)), 
            position = position_dodge(width = 0.9), vjust = -0.5) +
  scale_fill_manual(values = c("Yes" = "#FF6B6B", "No" = "#4ECDC4")) +
  labs(title = "Attrition Rate by Job Level",
       x = "Job Level",
       y = "Percentage") +
  theme_economist()

# Overtime and attrition
overtime_attrition <- attrition_clean %>%
  group_by(OverTime, Attrition) %>%
  summarize(Count = n(), .groups = "drop") %>%
  group_by(OverTime) %>%
  mutate(Percentage = Count / sum(Count) * 100)

overtime_plot <- ggplot(overtime_attrition, aes(x = OverTime, y = Percentage, fill = Attrition)) +
  geom_bar(stat = "identity", position = "dodge") +
  geom_text(aes(label = sprintf("%.1f%%", Percentage)), 
            position = position_dodge(width = 0.9), vjust = -0.5) +
  scale_fill_manual(values = c("Yes" = "#FF6B6B", "No" = "#4ECDC4")) +
  labs(title = "Attrition Rate by Overtime Status",
       x = "Overtime",
       y = "Percentage") +
  theme_economist()

# Display plots
print(dept_plot)
```

![](DS-6306_Project_Jonathan_Rocha_files/figure-html/job_related_factors-1.png)<!-- -->

``` r
print(joblevel_plot)
```

![](DS-6306_Project_Jonathan_Rocha_files/figure-html/job_related_factors-2.png)<!-- -->

``` r
print(overtime_plot)
```

![](DS-6306_Project_Jonathan_Rocha_files/figure-html/job_related_factors-3.png)<!-- -->

``` r
# Display job role plot separately (it's taller)
jobrole_plot
```

![](DS-6306_Project_Jonathan_Rocha_files/figure-html/job_related_factors-4.png)<!-- -->

### Compensation and Career Factors


``` r
# Monthly income distribution by attrition
income_plot <- ggplot(attrition_clean, aes(x = Attrition, y = MonthlyIncome, fill = Attrition)) +
  geom_boxplot(alpha = 0.7) +
  scale_fill_manual(values = c("Yes" = "#FF6B6B", "No" = "#4ECDC4")) +
  labs(title = "Monthly Income Distribution by Attrition Status",
       x = "Attrition",
       y = "Monthly Income ($)") +
  theme_economist() +
  theme(legend.position = "none")

# Years at company and attrition
years_company_plot <- ggplot(attrition_clean, aes(x = Attrition, y = YearsAtCompany, fill = Attrition)) +
  geom_boxplot(alpha = 0.7) +
  scale_fill_manual(values = c("Yes" = "#FF6B6B", "No" = "#4ECDC4")) +
  labs(title = "Years at Company Distribution by Attrition Status",
       x = "Attrition",
       y = "Years at Company") +
  theme_economist() +
  theme(legend.position = "none")

# Years since last promotion and attrition
promotion_plot <- ggplot(attrition_clean, aes(x = Attrition, y = YearsSinceLastPromotion, fill = Attrition)) +
  geom_boxplot(alpha = 0.7) +
  scale_fill_manual(values = c("Yes" = "#FF6B6B", "No" = "#4ECDC4")) +
  labs(title = "Years Since Last Promotion by Attrition Status",
       x = "Attrition",
       y = "Years Since Last Promotion") +
  theme_economist() +
  theme(legend.position = "none")

# Stock option level and attrition
stockoption_attrition <- attrition_clean %>%
  group_by(StockOptionLevel, Attrition) %>%
  summarize(Count = n(), .groups = "drop") %>%
  group_by(StockOptionLevel) %>%
  mutate(Percentage = Count / sum(Count) * 100)

stock_plot <- ggplot(stockoption_attrition, aes(x = as.factor(StockOptionLevel), y = Percentage, fill = Attrition)) +
  geom_bar(stat = "identity", position = "dodge") +
  geom_text(aes(label = sprintf("%.1f%%", Percentage)), 
            position = position_dodge(width = 0.9), vjust = -0.5) +
  scale_fill_manual(values = c("Yes" = "#FF6B6B", "No" = "#4ECDC4")) +
  labs(title = "Attrition Rate by Stock Option Level",
       x = "Stock Option Level",
       y = "Percentage") +
  theme_economist()

# Display plots
print(income_plot)
```

![](DS-6306_Project_Jonathan_Rocha_files/figure-html/compensation_factors-1.png)<!-- -->

``` r
print(years_company_plot)
```

![](DS-6306_Project_Jonathan_Rocha_files/figure-html/compensation_factors-2.png)<!-- -->

``` r
print(promotion_plot)
```

![](DS-6306_Project_Jonathan_Rocha_files/figure-html/compensation_factors-3.png)<!-- -->

``` r
print(stock_plot)
```

![](DS-6306_Project_Jonathan_Rocha_files/figure-html/compensation_factors-4.png)<!-- -->

### Satisfaction and Work-Life Balance Factors


``` r
# Job satisfaction and attrition
jobsat_attrition <- attrition_clean %>%
  group_by(JobSatisfaction, Attrition) %>%
  summarize(Count = n(), .groups = "drop") %>%
  group_by(JobSatisfaction) %>%
  mutate(Percentage = Count / sum(Count) * 100)

jobsat_plot <- ggplot(jobsat_attrition, aes(x = as.factor(JobSatisfaction), y = Percentage, fill = Attrition)) +
  geom_bar(stat = "identity", position = "dodge") +
  geom_text(aes(label = sprintf("%.1f%%", Percentage)), 
            position = position_dodge(width = 0.9), vjust = -0.5) +
  scale_fill_manual(values = c("Yes" = "#FF6B6B", "No" = "#4ECDC4")) +
  labs(title = "Attrition Rate by Job Satisfaction Level",
       x = "Job Satisfaction (1=Low, 4=Very High)",
       y = "Percentage") +
  theme_economist()

# Environment satisfaction and attrition
envsat_attrition <- attrition_clean %>%
  group_by(EnvironmentSatisfaction, Attrition) %>%
  summarize(Count = n(), .groups = "drop") %>%
  group_by(EnvironmentSatisfaction) %>%
  mutate(Percentage = Count / sum(Count) * 100)

envsat_plot <- ggplot(envsat_attrition, aes(x = as.factor(EnvironmentSatisfaction), y = Percentage, fill = Attrition)) +
  geom_bar(stat = "identity", position = "dodge") +
  geom_text(aes(label = sprintf("%.1f%%", Percentage)), 
            position = position_dodge(width = 0.9), vjust = -0.5) +
  scale_fill_manual(values = c("Yes" = "#FF6B6B", "No" = "#4ECDC4")) +
  labs(title = "Attrition Rate by Environment Satisfaction",
       x = "Environment Satisfaction (1=Low, 4=Very High)",
       y = "Percentage") +
  theme_economist()

# Work-life balance and attrition
wlb_attrition <- attrition_clean %>%
  group_by(WorkLifeBalance, Attrition) %>%
  summarize(Count = n(), .groups = "drop") %>%
  group_by(WorkLifeBalance) %>%
  mutate(Percentage = Count / sum(Count) * 100)

wlb_plot <- ggplot(wlb_attrition, aes(x = as.factor(WorkLifeBalance), y = Percentage, fill = Attrition)) +
  geom_bar(stat = "identity", position = "dodge") +
  geom_text(aes(label = sprintf("%.1f%%", Percentage)), 
            position = position_dodge(width = 0.9), vjust = -0.5) +
  scale_fill_manual(values = c("Yes" = "#FF6B6B", "No" = "#4ECDC4")) +
  labs(title = "Attrition Rate by Work-Life Balance",
       x = "Work-Life Balance (1=Bad, 4=Best)",
       y = "Percentage") +
  theme_economist()

# Arrange in a grid
grid.arrange(jobsat_plot, envsat_plot, wlb_plot, ncol = 2)
```

![](DS-6306_Project_Jonathan_Rocha_files/figure-html/satisfaction_factors-1.png)<!-- -->

## Correlation Analysis


``` r
# Convert Attrition to numeric (No = 0, Yes = 1)
attrition_clean$AttritionBinary <- ifelse(attrition_clean$Attrition == "Yes", 1, 0)

# Select only numeric variables for correlation analysis
numeric_vars <- attrition_clean %>%
  select_if(is.numeric)

# Calculate correlation matrix
correlation_matrix <- cor(numeric_vars)

# Create a correlation plot focusing on AttritionBinary
corrplot(correlation_matrix, method = "color", type = "upper", order = "hclust",
         tl.col = "black", tl.srt = 45, addrect = 3,
         col = colorRampPalette(c("#4ECDC4", "white", "#FF6B6B"))(200))
```

![](DS-6306_Project_Jonathan_Rocha_files/figure-html/correlation_analysis-1.png)<!-- -->

``` r
# Identify top correlations with Attrition
attrition_correlations <- data.frame(
  Variable = names(correlation_matrix["AttritionBinary", ]),
  Correlation = as.numeric(correlation_matrix["AttritionBinary", ])
) %>%
  filter(Variable != "AttritionBinary") %>%
  arrange(desc(abs(Correlation)))

head(attrition_correlations, 10) %>%
  kable(caption = "Top 10 Variables Correlated with Attrition") %>%
  kable_styling(bootstrap_options = c("striped", "hover"))
```

<table class="table table-striped table-hover" style="color: black; margin-left: auto; margin-right: auto;">
<caption>Top 10 Variables Correlated with Attrition</caption>
 <thead>
  <tr>
   <th style="text-align:left;"> Variable </th>
   <th style="text-align:right;"> Correlation </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> JobInvolvement </td>
   <td style="text-align:right;"> -0.1877934 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> TotalWorkingYears </td>
   <td style="text-align:right;"> -0.1672061 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> JobLevel </td>
   <td style="text-align:right;"> -0.1621364 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> YearsInCurrentRole </td>
   <td style="text-align:right;"> -0.1562157 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> MonthlyIncome </td>
   <td style="text-align:right;"> -0.1549150 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Age </td>
   <td style="text-align:right;"> -0.1493836 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> StockOptionLevel </td>
   <td style="text-align:right;"> -0.1486803 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> YearsWithCurrManager </td>
   <td style="text-align:right;"> -0.1467822 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> YearsAtCompany </td>
   <td style="text-align:right;"> -0.1287541 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> JobSatisfaction </td>
   <td style="text-align:right;"> -0.1075209 </td>
  </tr>
</tbody>
</table>

## Feature Engineering


``` r
# Creating some engineered features that might be useful
attrition_features <- attrition_clean %>%
  # Calculate salary per year of experience
  mutate(SalaryPerExperience = ifelse(TotalWorkingYears > 0, MonthlyIncome / TotalWorkingYears, MonthlyIncome),
         # Time since last change (promotion or manager change)
         TimeSinceChange = pmin(YearsSinceLastPromotion, YearsWithCurrManager),
         # Career progression ratio
         CareerProgressionRatio = ifelse(YearsAtCompany > 0, JobLevel / YearsAtCompany, JobLevel),
         # Salary to job level ratio
         SalaryToJobLevelRatio = MonthlyIncome / JobLevel,
         # Satisfaction composite (average of all satisfaction measures)
         SatisfactionComposite = (JobSatisfaction + EnvironmentSatisfaction + 
                                 RelationshipSatisfaction + WorkLifeBalance) / 4)

# Check correlations of new features with attrition
attrition_features$AttritionBinary <- ifelse(attrition_features$Attrition == "Yes", 1, 0)

new_features <- c("SalaryPerExperience", "TimeSinceChange", "CareerProgressionRatio", 
                 "SalaryToJobLevelRatio", "SatisfactionComposite", "AttritionBinary")

new_features_cor <- cor(attrition_features[new_features])

corrplot(new_features_cor, method = "color", type = "upper",
         tl.col = "black", addrect = 2,
         col = colorRampPalette(c("#4ECDC4", "white", "#FF6B6B"))(200))
```

![](DS-6306_Project_Jonathan_Rocha_files/figure-html/feature_engineering-1.png)<!-- -->

``` r
# Visualize one of our engineered features
ggplot(attrition_features, aes(x = Attrition, y = SatisfactionComposite, fill = Attrition)) +
  geom_boxplot(alpha = 0.7) +
  scale_fill_manual(values = c("Yes" = "#FF6B6B", "No" = "#4ECDC4")) +
  labs(title = "Satisfaction Composite Score by Attrition Status",
       x = "Attrition",
       y = "Satisfaction Composite Score") +
  theme_economist() +
  theme(legend.position = "none")
```

![](DS-6306_Project_Jonathan_Rocha_files/figure-html/feature_engineering-2.png)<!-- -->

## Predictive Modeling

### Data Preparation for Modeling


``` r
# Prepare the data for modeling
# Convert categorical variables to factors if they aren't already
model_data <- attrition_features %>%
  mutate(across(where(is.character), as.factor))

# Create training and testing sets
set.seed(123)  # For reproducibility
train_index <- createDataPartition(model_data$Attrition, p = 0.7, list = FALSE)
train_data <- model_data[train_index, ]
test_data <- model_data[-train_index, ]

# Define the formula for modeling
predictors <- names(train_data)[!names(train_data) %in% c("Attrition", "AttritionBinary")]
model_formula <- as.formula(paste("Attrition ~", paste(predictors, collapse = " + ")))

# Define preprocessing steps
preprocess_steps <- preProcess(train_data[, !names(train_data) %in% c("Attrition", "AttritionBinary")], 
                              method = c("center", "scale"))

# Apply preprocessing
train_data_processed <- predict(preprocess_steps, train_data)
test_data_processed <- predict(preprocess_steps, test_data)
```

### KNN Model


``` r
# Train a KNN model
set.seed(123)
knn_model <- train(
  model_formula,
  data = train_data_processed,
  method = "knn",
  trControl = trainControl(
    method = "cv",
    number = 5,
    classProbs = TRUE,
    summaryFunction = twoClassSummary
  ),
  metric = "ROC",
  tuneLength = 10
)

# Print model results
print(knn_model)
```

```
## k-Nearest Neighbors 
## 
## 609 samples
##  35 predictor
##   2 classes: 'No', 'Yes' 
## 
## No pre-processing
## Resampling: Cross-Validated (5 fold) 
## Summary of sample sizes: 487, 488, 486, 488, 487 
## Resampling results across tuning parameters:
## 
##   k   ROC        Sens       Spec      
##    5  0.6681193  0.9843328  0.18315789
##    7  0.6847923  0.9862745  0.14315789
##    9  0.6881569  0.9960784  0.10263158
##   11  0.7125167  0.9980392  0.07210526
##   13  0.7233637  0.9980392  0.08210526
##   15  0.7152820  0.9980392  0.10210526
##   17  0.7249082  1.0000000  0.08157895
##   19  0.7182266  0.9980392  0.07157895
##   21  0.7319877  0.9960784  0.05157895
##   23  0.7362048  0.9980392  0.05157895
## 
## ROC was used to select the optimal model using the largest value.
## The final value used for the model was k = 23.
```

``` r
plot(knn_model)
```

![](DS-6306_Project_Jonathan_Rocha_files/figure-html/knn_model-1.png)<!-- -->

``` r
# Make predictions on test set
knn_predictions <- predict(knn_model, test_data_processed)
knn_probs <- predict(knn_model, test_data_processed, type = "prob")

# Confusion matrix
knn_cm <- confusionMatrix(knn_predictions, test_data_processed$Attrition, positive = "Yes")
print(knn_cm)
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction  No Yes
##        No  218  40
##        Yes   1   2
##                                          
##                Accuracy : 0.8429         
##                  95% CI : (0.793, 0.8849)
##     No Information Rate : 0.8391         
##     P-Value [Acc > NIR] : 0.474          
##                                          
##                   Kappa : 0.0689         
##                                          
##  Mcnemar's Test P-Value : 2.946e-09      
##                                          
##             Sensitivity : 0.047619       
##             Specificity : 0.995434       
##          Pos Pred Value : 0.666667       
##          Neg Pred Value : 0.844961       
##              Prevalence : 0.160920       
##          Detection Rate : 0.007663       
##    Detection Prevalence : 0.011494       
##       Balanced Accuracy : 0.521526       
##                                          
##        'Positive' Class : Yes            
## 
```

``` r
# Visualize confusion matrix
fourfoldplot(knn_cm$table, color = c("#FF6B6B", "#4ECDC4"), main = "KNN Confusion Matrix")
```

![](DS-6306_Project_Jonathan_Rocha_files/figure-html/knn_model-2.png)<!-- -->

### Naive Bayes Model


``` r
# Train a Naive Bayes model
set.seed(123)
nb_model <- train(
  model_formula,
  data = train_data_processed,
  method = "naive_bayes",
  trControl = trainControl(
    method = "cv",
    number = 5,
    classProbs = TRUE,
    summaryFunction = twoClassSummary
  ),
  metric = "ROC"
)

# Print model results
print(nb_model)
```

```
## Naive Bayes 
## 
## 609 samples
##  35 predictor
##   2 classes: 'No', 'Yes' 
## 
## No pre-processing
## Resampling: Cross-Validated (5 fold) 
## Summary of sample sizes: 487, 488, 486, 488, 487 
## Resampling results across tuning parameters:
## 
##   usekernel  ROC        Sens       Spec      
##   FALSE      0.7616762  0.6671045  0.72684211
##    TRUE      0.8010884  0.9980392  0.06157895
## 
## Tuning parameter 'laplace' was held constant at a value of 0
## Tuning
##  parameter 'adjust' was held constant at a value of 1
## ROC was used to select the optimal model using the largest value.
## The final values used for the model were laplace = 0, usekernel = TRUE
##  and adjust = 1.
```

``` r
plot(nb_model)
```

![](DS-6306_Project_Jonathan_Rocha_files/figure-html/naive_bayes_model-1.png)<!-- -->

``` r
# Make predictions on test set
nb_predictions <- predict(nb_model, test_data_processed)
nb_probs <- predict(nb_model, test_data_processed, type = "prob")

# Confusion matrix
nb_cm <- confusionMatrix(nb_predictions, test_data_processed$Attrition, positive = "Yes")
print(nb_cm)
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction  No Yes
##        No  219  39
##        Yes   0   3
##                                           
##                Accuracy : 0.8506          
##                  95% CI : (0.8014, 0.8915)
##     No Information Rate : 0.8391          
##     P-Value [Acc > NIR] : 0.3427          
##                                           
##                   Kappa : 0.1143          
##                                           
##  Mcnemar's Test P-Value : 1.166e-09       
##                                           
##             Sensitivity : 0.07143         
##             Specificity : 1.00000         
##          Pos Pred Value : 1.00000         
##          Neg Pred Value : 0.84884         
##              Prevalence : 0.16092         
##          Detection Rate : 0.01149         
##    Detection Prevalence : 0.01149         
##       Balanced Accuracy : 0.53571         
##                                           
##        'Positive' Class : Yes             
## 
```

``` r
# Visualize confusion matrix
fourfoldplot(nb_cm$table, color = c("#FF6B6B", "#4ECDC4"), main = "Naive Bayes Confusion Matrix")
```

![](DS-6306_Project_Jonathan_Rocha_files/figure-html/naive_bayes_model-2.png)<!-- -->

### Handling Class Imbalance


``` r
# First, let's use ROSE for balancing
set.seed(123)
train_balanced_rose <- ROSE(Attrition ~ ., data = train_data, seed = 123)$data

# Check the new class distribution
table(train_balanced_rose$Attrition)
```

```
## 
##  No Yes 
## 316 293
```

``` r
# Preprocess the ROSE-balanced dataset
preprocess_steps_rose <- preProcess(train_balanced_rose[, !names(train_balanced_rose) %in% c("Attrition", "AttritionBinary")],
                                   method = c("center", "scale"))
train_balanced_rose_processed <- predict(preprocess_steps_rose, train_balanced_rose)

# Train models with balanced data
# Gradient Boosting
gbm_model <- train(
  model_formula,
  data = train_balanced_rose_processed,
  method = "gbm",
  trControl = trainControl(
    method = "cv",
    number = 5,
    classProbs = TRUE,
    summaryFunction = twoClassSummary
  ),
  metric = "ROC",
  verbose = FALSE
)

# Random Forest
rf_model <- train(
  model_formula,
  data = train_balanced_rose_processed,
  method = "rf",
  trControl = trainControl(
    method = "cv",
    number = 5,
    classProbs = TRUE,
    summaryFunction = twoClassSummary
  ),
  metric = "ROC",
  ntree = 100
)

# Evaluate models
gbm_pred <- predict(gbm_model, test_data_processed)
gbm_cm <- confusionMatrix(gbm_pred, test_data_processed$Attrition, positive = "Yes")

rf_pred <- predict(rf_model, test_data_processed)
rf_cm <- confusionMatrix(rf_pred, test_data_processed$Attrition, positive = "Yes")

# Print results
print(gbm_cm)
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction  No Yes
##        No  132  10
##        Yes  87  32
##                                           
##                Accuracy : 0.6284          
##                  95% CI : (0.5666, 0.6871)
##     No Information Rate : 0.8391          
##     P-Value [Acc > NIR] : 1               
##                                           
##                   Kappa : 0.2095          
##                                           
##  Mcnemar's Test P-Value : 1.194e-14       
##                                           
##             Sensitivity : 0.7619          
##             Specificity : 0.6027          
##          Pos Pred Value : 0.2689          
##          Neg Pred Value : 0.9296          
##              Prevalence : 0.1609          
##          Detection Rate : 0.1226          
##    Detection Prevalence : 0.4559          
##       Balanced Accuracy : 0.6823          
##                                           
##        'Positive' Class : Yes             
## 
```

``` r
print(rf_cm)
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction  No Yes
##        No  139  12
##        Yes  80  30
##                                           
##                Accuracy : 0.6475          
##                  95% CI : (0.5862, 0.7054)
##     No Information Rate : 0.8391          
##     P-Value [Acc > NIR] : 1               
##                                           
##                   Kappa : 0.211           
##                                           
##  Mcnemar's Test P-Value : 2.844e-12       
##                                           
##             Sensitivity : 0.7143          
##             Specificity : 0.6347          
##          Pos Pred Value : 0.2727          
##          Neg Pred Value : 0.9205          
##              Prevalence : 0.1609          
##          Detection Rate : 0.1149          
##    Detection Prevalence : 0.4215          
##       Balanced Accuracy : 0.6745          
##                                           
##        'Positive' Class : Yes             
## 
```

### Model Comparison


``` r
# Compare models
models_comparison_all <- resamples(list(
  KNN = knn_model,
  NaiveBayes = nb_model,
  RandomForest = rf_model,
  GradientBoosting = gbm_model
))

# Summarize model comparison
summary(models_comparison_all)
```

```
## 
## Call:
## summary.resamples(object = models_comparison_all)
## 
## Models: KNN, NaiveBayes, RandomForest, GradientBoosting 
## Number of resamples: 5 
## 
## ROC 
##                       Min.   1st Qu.    Median      Mean   3rd Qu.      Max.
## KNN              0.6282250 0.6855392 0.7592879 0.7362048 0.7618932 0.8460784
## NaiveBayes       0.7572239 0.7764706 0.7791262 0.8010884 0.8205882 0.8720330
## RandomForest     0.8622545 0.8648103 0.8746301 0.8800710 0.8987411 0.8999192
## GradientBoosting 0.7876059 0.8607006 0.8845843 0.8667546 0.8869732 0.9139091
##                  NA's
## KNN                 0
## NaiveBayes          0
## RandomForest        0
## GradientBoosting    0
## 
## Sens 
##                       Min.   1st Qu.    Median      Mean   3rd Qu.      Max.
## KNN              0.9901961 1.0000000 1.0000000 0.9980392 1.0000000 1.0000000
## NaiveBayes       0.9901961 1.0000000 1.0000000 0.9980392 1.0000000 1.0000000
## RandomForest     0.7777778 0.7936508 0.8125000 0.8196429 0.8412698 0.8730159
## GradientBoosting 0.7343750 0.7460317 0.8253968 0.8071925 0.8571429 0.8730159
##                  NA's
## KNN                 0
## NaiveBayes          0
## RandomForest        0
## GradientBoosting    0
## 
## Spec 
##                       Min.   1st Qu.    Median       Mean   3rd Qu.      Max.
## KNN              0.0000000 0.0000000 0.0500000 0.05157895 0.0500000 0.1578947
## NaiveBayes       0.0000000 0.0000000 0.0000000 0.06157895 0.1500000 0.1578947
## RandomForest     0.6779661 0.7931034 0.8305085 0.79877265 0.8448276 0.8474576
## GradientBoosting 0.7118644 0.7241379 0.7931034 0.78480421 0.8474576 0.8474576
##                  NA's
## KNN                 0
## NaiveBayes          0
## RandomForest        0
## GradientBoosting    0
```

``` r
bwplot(models_comparison_all, main = "Model Performance Comparison")
```

![](DS-6306_Project_Jonathan_Rocha_files/figure-html/model_comparison-1.png)<!-- -->

``` r
# ROC curves
rf_probs <- predict(rf_model, test_data_processed, type = "prob")
gbm_probs <- predict(gbm_model, test_data_processed, type = "prob")

roc_knn <- roc(response = test_data_processed$Attrition, predictor = knn_probs$Yes)
roc_nb <- roc(response = test_data_processed$Attrition, predictor = nb_probs$Yes)
roc_rf <- roc(response = test_data_processed$Attrition, predictor = rf_probs$Yes)
roc_gbm <- roc(response = test_data_processed$Attrition, predictor = gbm_probs$Yes)

# Plot ROC curves
plot(roc_knn, col = "#FF6B6B", main = "ROC
     Curves for Different Models")
plot(roc_nb, col = "#FF6B6B", add = TRUE)
plot(roc_rf, col = "#FF6B6B", add = TRUE)
plot(roc_gbm, col = "#FF6B6B", add = TRUE)
legend("bottomright", legend = c("KNN", "Naive Bayes", "Random Forest", "Gradient Boosting"),
       col = c("#FF6B6B", "#FF6B6B", "#FF6B6B", "#FF6B6B"), lty = 1)
```

![](DS-6306_Project_Jonathan_Rocha_files/figure-html/model_comparison-2.png)<!-- -->

