# Employee Attrition Analysis - DDSAnalytics Case Study for Frito Lay

## Executive Summary

DDSAnalytics was hired by Frito Lay to identify factors related to employee attrition and build predictive models to help reduce turnover costs. According to research, replacing an employee costs between 50% and 400% of their salary, while targeted retention incentives cost approximately $200 per employee.

### Key Findings:

1. **Top three factors contributing to attrition:**
   - **Overtime**: Employees working overtime have a significantly higher attrition rate (31.0%) compared to those who don't (10.0%), making this the strongest predictor of turnover
   - **Job Level**: Entry-level positions (Level 1) experience much higher attrition rates (23.8%) than higher levels
   - **Total Working Years**: Less experienced employees, particularly those with fewer than 5 years total working experience, are substantially more likely to leave

2. **Model Performance:**
   - Our predictive models achieved 67.2% sensitivity and 65.3% specificity, exceeding the required 60% threshold
   - The gradient boosting model outperformed both KNN and Naive Bayes models

3. **Financial Impact:**
   - Implementing the model could save Frito Lay approximately $487,000 annually in attrition-related costs (at the mid-range replacement cost estimate)
   - ROI increases significantly when using the model to target retention efforts at employees most likely to leave

4. **Recommendations:**
   - Implement targeted retention strategies for employees working overtime
   - Develop career progression pathways for entry-level employees
   - Enhance onboarding and support for employees with less total working experience
   - Deploy the predictive model to proactively identify employees at risk of leaving

## Repository Structure

- **Analysis Code:** 
  - `CaseStudy1.Rmd`: Complete R Markdown file with all analysis code and documentation
  - `CaseStudy1.html`: Knitted HTML output of the R Markdown analysis
  
- **Presentation Materials:**
  - `Employee_Attrition_Analysis.pptx`: PowerPoint presentation for Frito Lay executives
  - [YouTube Presentation Link](https://youtu.be/example) - 7-minute summary of findings

- **Model Predictions:**
  - `Case1PredictionsYourName Attrition.csv`: Predictions for the 300 competition set employees

- **Data Files:**
  - `CaseStudy1data.csv`: Original dataset with 870 employee records and attrition information
  - `CaseStudy1CompSet No Attrition.csv`: Competition dataset with 300 employee records (no attrition data)

## Methods

Our analysis followed a structured data science approach:

1. **Data preparation and cleaning:** Reviewed data quality and prepared it for analysis
2. **Exploratory data analysis (EDA):** Identified patterns and relationships with attrition
3. **Feature engineering:** Created derived variables to improve model performance  
4. **Model development:** Built and compared KNN, Naive Bayes, and ensemble models
5. **Cost-benefit analysis:** Quantified the potential financial impact of using the model
6. **Competition predictions:** Generated attrition predictions for the unlabeled dataset

## Key Insights

### Attrition by Overtime Status
Overtime emerged as the strongest predictor of employee attrition. Employees who work overtime are 3.1 times more likely to leave than those who don't.

### Attrition by Job Level
A clear inverse relationship exists between job level and attrition, with lower-level positions experiencing substantially higher turnover rates.

### Attrition by Total Working Years
Employees with fewer total working years (less overall work experience) demonstrate significantly higher attrition rates than more experienced employees.

## Model Performance

Our final gradient boosting model achieved:
- **Sensitivity:** 67.2% (ability to identify employees who will leave)
- **Specificity:** 65.3% (ability to identify employees who will stay)
- **Accuracy:** 65.8% (overall correct predictions)

This performance exceeds the required thresholds of 60% for both sensitivity and specificity.

## Financial Impact

By implementing the predictive model and targeting retention efforts at employees identified as high-risk for attrition, Frito Lay could realize:

- **Cost without model:** $1,823,400 (annual attrition replacement costs)
- **Cost with model:** $1,336,200 (retention incentives + remaining attrition costs)
- **Annual savings:** $487,200 (26.7% reduction in attrition-related expenses)

## Author
Jonathan Rocha
