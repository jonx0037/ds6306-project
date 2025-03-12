# Employee Attrition Analysis for Frito Lay

## Executive Summary

DDSAnalytics was engaged by Frito Lay to identify factors related to employee attrition and develop predictive models to reduce turnover costs. This analysis explores patterns in attrition, builds machine learning models to predict which employees might leave, and quantifies the potential cost savings of implementing these models.

### Key Findings

1. **Overall Situation**: Frito Lay has a 16.1% attrition rate (140 of 870 employees), with replacement costs ranging from 50-400% of annual salary.

2. **Top 3 Factors Contributing to Attrition**:
   - **Overtime**: Employees working overtime have 3.3× higher attrition (31.7% vs. 9.7%)
   - **Total Working Years**: Less experienced employees are significantly more likely to leave (correlation: -0.167)
   - **Job Level/Monthly Income**: Entry-level positions have 26.1% attrition vs. 5-10% for higher levels

3. **Model Performance**:
   - Our gradient boosting model achieved 69.1% sensitivity and 69.4% specificity
   - This exceeds the requirement of 60% for both metrics
   - The model effectively balances identifying potential leavers while minimizing false alarms

4. **Financial Impact**:
   - Implementing the model could save approximately $5 million (69%) in attrition-related costs
   - This is based on a mid-range replacement cost estimate (225% of annual salary)
   - ROI increases with higher replacement cost estimates

5. **Additional Insights**:
   - Single employees have higher attrition (25.6%) compared to married (10.5%) or divorced (6.3%)
   - Sales Representatives have the highest attrition rate (45.3%)
   - Poor work-life balance is associated with higher turnover

### Recommendations

1. Target retention efforts at employees working overtime
2. Develop mentoring and support programs for less experienced employees
3. Review compensation and career advancement opportunities for entry-level positions
4. Implement the predictive model to guide proactive retention interventions
5. Monitor effectiveness and refine approach over time

## Repository Contents

1. **CaseStudy1.Rmd** - R Markdown file with complete analysis
2. **CaseStudy1.html** - Knitted HTML output of the analysis
3. **Employee_Attrition_Analysis.pptx** - PowerPoint presentation for stakeholders
4. **Case1PredictionsYOURLASTNAME Attrition.csv** - Attrition predictions for competition dataset
5. **data/** - Directory containing datasets used in the analysis
6. **visualizations/** - Directory containing saved plots for the presentation

## Video Presentation

The 7-minute presentation to Frito Lay's CEO and CFO can be viewed at: [YouTube/Zoom Link]

## Author

[Your Name]

## Course Information

MSDS 6306: Doing Data Science  
Southern Methodist University  
March 9, 2025
