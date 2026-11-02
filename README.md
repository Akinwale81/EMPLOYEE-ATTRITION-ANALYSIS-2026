# EMPLOYEE-ATTRITION-ANALYSIS-2026

## PROJECT OVERVIEW
This project explains why 895 of 5,000 employees (17.9%) left the workforce in 2026, using a SQL Server source table and a five-page Power BI dashboard.
The analysis starts from a single employee-level table of 42 fields covering demographics, job and pay, tenure, workload, engagement scores and the attrition outcome. Data is shaped in Power Query, modelled with DAX measures, and delivered as an interactive report (Employee_Attrition_WIP.pbix) that HR and business leaders can filter by department, work mode, gender, education and workload bands.

## OBJECTIVES
• Measure attrition overall and by department, job role, job level, gender and work mode.
• Identify which working conditions (burnout, overtime, work-life balance, manager support, commute) are linked to leaving.
• Test whether pay, salary hikes, stock options and promotion timing are linked to leaving.
• Take a closer look at Customer Support, the department with the highest attrition rate.

## BUSINESS PROBLEMS
Losing almost one in five employees is costly, and leadership needs to know where it happens, why, and what to fix first. The dashboard is built to answer six questions, one per page or panel:
• How big is the problem? Total workforce, retained and attrited headcount, and attrition and retention rates.
• Where is attrition concentrated? Which departments, job roles, job levels, genders and work modes lose the most people.
• What working conditions push people out? Burnout, work-life balance, weekly overtime, commute time and manager support.
• Is pay or career growth a factor? Salary hike percentage, stock option level, years since promotion, and the income gap between leavers and stayers.
• What is happening in Customer Support? Why the department with the highest attrition rate loses people, and which workload factor matters most there.
• Where should HR act first? Which segments combine a high rate with enough headcount to matter.

## DATASETS
The project uses one table, employee_attrition_hr_2026, held in the employee_attrition SQL Server database (schema dbo). It has 5,000 rows, one per employee, with a unique employee_id and no missing values. The outcome field attrition splits the workforce into 4,105 Retained and 895 Attrited employees.


-----------------------|---------------------------------------------------------------------------------------------|
FSGSFSHSKK


