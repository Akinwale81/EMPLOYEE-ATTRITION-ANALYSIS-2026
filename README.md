# EMPLOYEE-ATTRITION-ANALYSIS-2026

## PROJECT OVERVIEW
This project analyses why employees leave a 5,000-person organisation, and shows that 17.9% (895 employees) attrited, with burnout, overtime, weak manager support and poor work-life balance as the strongest warning signs. The exploratory analysis was done in SQL Server and the findings were turned into a five-page, interactive Power BI dashboard for HR leaders.

The work follows a standard analytics pipeline:

- Clean the HR table and engineer banded (categorical) features in SQL Server.
- Run a structured exploratory analysis of attrition rate by every demographic, job, compensation and wellbeing dimension.
- Import the prepared table into Power BI, apply a set of corrections, and build a DAX measure layer.
- Publish findings across five report pages: Workforce Overview, Attrition Driver, Attrition Hotspot, Compensation and Career Growth, and a Customer Support Deep Dive.

|HEADLINE METRIC                            |VALUE                                                               |
|-------------------------------------------|--------------------------------------------------------------------|
|Total Employees                            |5000                                                                |
|Retained                                   |4,105 (82.1%)                                                       |
|Attrited                                   |895 (17.9%)                                                         |
|Highest-attrition department               |Customer Support (24.1%)                                            |
|Average monthly income                     |6,991                                                               |

## BUSINESS PROBLEMS
Almost one in five employees leaves, and the organisation cannot yet say who is at risk, why, or where to intervene first. Replacing staff is costly in recruitment, onboarding and lost productivity, and attrition is far from evenly spread. HR leadership needs answers to five questions:
- How big is the problem? What are the overall retention and attrition rates, and which departments, roles and job levels carry the highest rates?
- Who is leaving? Do age, gender, marital status, education, tenure or work mode separate leavers from stayers?
- Why are they leaving? Which workload and wellbeing factors (burnout, overtime, work-life balance, manager support, commute) are most associated with exits?
- Is pay or career growth a factor? Do salary hikes, stock options, income and time since promotion differ between leavers and stayers?
- Where should HR act first? The Customer Support department has the highest attrition, so what is driving it there?

## OBJECTIVES
The project set out to turn the raw Employee attrition table into evidence HR can act on. Each objective maps to a business problem above.
- Baseline: quantify total employees, retention count and rate, and attrition count and rate.
- Profile: measure attrition by age, gender, marital status, education, department, job role, job level, work mode and tenure.
- Drivers: test the link between attrition and burnout, overtime, work-life balance, manager support and commute.
- Reward and growth: measure attrition by salary hike, stock option level and years since promotion, and compare leaver and stayer income by job level.
- Hotspot: isolate Customer Support and identify which factors push its attrition above the company average.
- Prepare: engineer clean, ordered, human-readable categories so results can be compared across dimensions.
- Model: build reusable DAX measures so every KPI, chart and "highest category" callout updates with the slicers.
- Communicate: deliver a consistent, filterable five-page dashboard and a written report with recommendations.

## DATASETS
The analysis uses a single table, employee_attrition_hr_2026, with one row per employee (5,000 unique employee_id values, no missing values) and 29 source columns. The target variable is attrition (0 = retained, 1 = attrited in the SQL layer). The dataset contains 8 departments, 25 job roles, 5 job levels, and three work modes (Onsite 1,742, Hybrid 2,237, Remote 1,021).

|GROUP                                    |COLUMNS                                                                          |
|-----------------------------------------|---------------------------------------------------------------------------------|
|Identity and demographics                |employee_id, age (21 to 64), gender, marital_status, education_level             |
|Role and structure                       |department, job_role, job_level, work_mode                                       |
|Compensation and reward                  |monthly_income, stock_option_level, salary_hike_pct (0 to 22.6)                  |
|Tenure and career                        |years_at_company, total_working_years,years_since_promotion,num_prior_companies,|  performance_rating, last_review_score|
|Workload                                 |overtime_hours_per_week (0 to 34.9), commute_minutes (0 to 92),business_travel_days_per_year, training_hours |
|Wellbeing and sentiment (1 to 10 scales) |manager_support_score, burnout_score, engagement_score, work_life_balance_score  |
|AI exposure                              |uses_ai_tools_at_work, perceived_ai_job_risk                                     |
|Target                                   |attrition                                                                       | 

The Power BI version of the table holds 42 columns: the 29 source columns, 7 SQL-derived banded columns, 4 banded score columns re-created in Power BI (manager support, burnout, engagement, work-life balance), and 2 sort-helper columns.

## TOOLS AND TECHNOLOGIES
|TOOL                                    |USED FOR                                                                          |
|----------------------------------------|----------------------------------------------------------------------------------|
|Microsoft SQL Server                    |Database creation, type conversion, CASE-based feature engineering, aggregate EDA queries |
|Power BI Desktop                        |Data model, report pages, slicers, cards, charts, navigation buttons             |
|DAX                                     |Measures (counts, rates, ranking labels), calculated columns for sort order      |
|Power BI data load and transform        |Import of the SQL table and the corrections described in data preparation        |

Key SQL server techniques: ALTER TABLE ... ALTER COLUMN, UPDATE ... SET ... CASE, conditional counting with COUNT(CASE WHEN ...), CAST ... AS FLOAT for safe division, and GROUP BY ... ORDER BY for ranked output. Key DAX techniques: CALCULATE, DIVIDE, ADDCOLUMNS, TOPN, CONCATENATEX, MAXX, VALUES, SWITCH(TRUE()), and variables (VAR).

## DATA PREPARATION
Preparation happened in two stages: feature engineering in SQL Server, then a set of corrections when the table was exported to Power BI. The corrections matter because they change how several categories read and sort in the final dashboard.

###  SQL Server preparation
- Database and table. A database employee_attrition was created and the table employee_attrition_hr_2026 inspected with SELECT *.
- Precision control. Eleven numeric columns (salary_hike_pct, years_at_company, total_working_years, years_since_promotion, overtime_hours_per_week, manager_support_score, burnout_score, engagement_score, work_life_balance_score, last_review_score, perceived_ai_job_risk) were converted to DECIMAL(10,1) so every value carries one decimal place.
- Banded features. Eleven categorical columns were added with ALTER TABLE ... ADD and populated with UPDATE ... CASE, turning continuous measures into groups that can be compared:

|NEW COLUMN                        |BAND RULE                                                                               |
|----------------------------------|----------------------------------------------------------------------------------------|
|stock_option_level_category       |0 = No stock option, 1 = Low, 2 = Medium, 3 = High                                      |
|salary_hike_pct_category          |up to 7.5 Low, up to 15 Medium, up to 22.6 High                                         |
|weekly_overtime_category          |up to 10 hrs Low, up to 20 Moderate, above 20 High                                      |
|years_since_promotion_category    |up to 4 Recently Promoted, up to 8 Due, above 8 Overdue                                 |
|age_category                      |21-35, 36-50, 50-64                                                                     |
|years_at_company_category         |up to 5, up to 10, up to 15, above 15                                                   |
|num_prior_companies_category      |up to 2, up to 4, up to 6, above 6                                                      |
|commute_minutes_category / manager_support / burnout|       |up to 20, 21-40, 41-60, 61-80, above 80 |
|work_life_balance_score_category|up to 3, up to 7, above 7 (scored 1 to 10) | 

 ### Corrections applied on export to Power BI
The table in the Power BI model differs from the SQL output in the following ways. Each change fixes an ambiguity in the SQL labels or makes the dashboard easier to read.

|FIELD                        |IN SQL                               |CORRECTION IN POWER BI                                 |
|-----------------------------|-------------------------------------|-------------------------------------------------------|
|attrition                    |0/1                                  |Text values "Retained" / "Attrited"                    |
|years_at_company_category    |0-5, 5-10, 10-15, 15-20              |0-5, 6-10, 11-15, 16-20                                |
|Sort order                   |Alphabetical by label                |Helper columns Commute Minutes Category Sort and Stock   Option Level Sort|

## EXPLORATORY DATA ANALYSIS
The EDA used one repeated query pattern: count employees, count attrition with COUNT(CASE WHEN attrition = 1 ...), and compute the rate as attrition divided by headcount, ordered from highest to lowest rate. Running the same pattern across every dimension makes the results directly comparable.

### Query structure
- Baseline: Total employees, retained and attrited counts, and retention and attrition rates.
- Company-wide profile: Age, gender, marital status, education, department, job role, job level, work mode, tenure, prior companies and performance rating.
- Driver analysis: Burnout, work-life balance, manager support, commute and weekly overtime categories, plus salary hike, stock options and years since promotion.
- Customer Support drill-down: The same driver queries repeated with WHERE department = 'Customer Support', together with department-level income and AI-tool usage cuts.

  ### Attrition rate by dimension (highest and lowest group)
  |DIMENTION                            |HIGHEST ATTRITION                        |LOWEST ATTRITION                         |
  |-------------------------------------|-----------------------------------------|-----------------------------------------|
  |Burnout band                         |High, 54.0% (161 employees)              |Low, 4.6% (1,356 employees)              |
  |Work-life balance band               |Poor, 52.7% (74 employees)               |High, 9.8% (1,177 employees)             |
  |Weekly overtime                      |High (over 20 hrs), 52.4% (63 employees) |Low (up to 10 hrs),14.9% (4,017 employees|
  |Manager support band                 |Low, 43.8% (160 employees)               |High, 10.1% (2,117 employees)            |
  |Salary hike                          |Low (up to 7.5%), 28.5% (621 employees)  |High, 11.4% (753 employees)              |
  |Years since promotion                |Overdue (over 8 yrs),33.3% (33 employees)|Recently promoted,16.8% (4,460 employees)|
  |Department                           |Customer Support, 24.1% (636 employees)  |Engineering, 14.5% (1,336 employees)     |
  |Work mode                            |Onsite, 21.2% (1,742 employees)          |Remote, 14.7% (1,021 employees)          |
  |Commute                              |61-80 min, 21.2% (273 employees)         |0-20 min, 15.4% (1,549 employees)        |
  |Stock options                        |None, 19.8% (1,528 employees)            |High, 16.7% (460 employees)              |
  |Gender                               |Female, 18.2% (2,310 employees)          |Non-binary, 13.0% (100 employees)        |

  ## DATA MODELLING AND DAX
  The model is a single flat fact table, employee_attrition_hr_2026, plus a dedicated measures table, Measures (2). With one  row per employee and every attribute on that row, no relationships or dimension tables are needed, and every slicer filters  every visual on its page directly.

  ###  Measure library
  |CATEGORY                       |MEASURES                                                                                 |
  |-------------------------------|-----------------------------------------------------------------------------------------|
  |Core KPIs                      |Total Employee, Total Attrited, Total Retained, Attrited Rate, Retained %, Avg Monthly       Income, Average Age |                                                                                                     
  |Highest callouts               |Highest Department by Attrition Rate, Highest Job Role by Attrition Rate, Highest Work       Mode by Attrition Rate, Highest Attrition Rate by Salary Hike PCT, Highest Attrition Rate by Years Since Promotion |      
  |Compensation comparison        |AVG Income Leavers, AVG Income Stayers, Income Gap, Income Gap %                         |
  |Customer Support               |Customer Support Employee Count, Attrition, Attrition Rate, Retention Count, Retention       Rate, Top Customer Support Attrition Driver |                                                                               

  Two calculated columns, Commute Minutes Category Sort and Stock Option Level Sort, use SWITCH(TRUE()) to give bands a logical display order.

  ## DASHBOARD DEVELOPMENT
  The dashboard has five pages that move from "how big is the problem" to "why" to "where" to "what about pay and growth" to "what is happening in the worst department". Every page shares a header ("Employee Attrition Analysis, HR Workforce Analytics 2026"), a left-hand title panel, a row of four slicers and a consistent KPI-card and chart layout, so the pages read as one report.

  |PAGE             |QUESTION ANSWERED               |VISUALS                           |SLICERS                            |
  |-----------------|--------------------------------|----------------------------------|-----------------------------------|
  | Workforce Overview | How big is attrition and where does it sit? | Cards: total workforce, retention rate, retained, attrition rate, attrited, average burnout, average monthly income. Charts: attrition by department, job role, job level, gender | Commute, work-life balance, burnout, overtime |
  | Attrition Driver | Which wellbeing and workload factors drive exits? |Attrition by burnout, work-life balance and commute bands; line chart of burnout band by overtime band; four "highest attrition" callouts (burnout, commute, manager support, work-life balance) | Department, job role, gender, work mode |
  | Attrition Hotspot |Which groups are worst affected? | Attrition by department, job level, job role and work mode; four "highest" callouts | Work-life balance, commute, burnout, overtime |
  | Compensation and Career Growth | Do pay and progression matter? | Average income of leavers vs stayers by job level; attrition by salary hike, years since promotion and stock option level; callouts including the income gap | Department, work mode, job role, education |
  | Customer Support Deep Dive | What is driving the highest-attrition department? | Customer Support employee count, attrition and retention counts and rates; attrition by burnout, overtime, work-life balance and commute; top-driver callout    | Work mode, gender, education, marital status |
                                                      
### Design and usability decisions
- Fixed canvas and grid. Pages 2 to 5 use an identical 2,300 by 1,300 layout with the same slicer strip, card column and four-chart grid, which lets users compare pages without relearning the layout.
- Text callouts as measures. "Highest X by attrition" cards give the answer in one glance and stay live under slicers.
- Tooltips are designed to carry volume. Rate charts show attrition count and headcount in the tooltip, so a high percentage can be judged against the size of the group behind it (see Limitations for the headcount field to correct).
- Logical ordering. Sort helper columns keep bands such as commute minutes and stock option level in natural order.
- Navigation. Page-navigation buttons sit in the left panel of the overview page.

  ## INSIGHTS
  The data points to a workload-and-wellbeing problem more than a demographic or pay problem. Twelve findings, ranked by strength and usefulness:

  |Insight                       |Explanation                                                                              |
  |------------------------------|-----------------------------------------------------------------------------------------|
  |Burnout is the strongest single warning sign.| Attrition is 54.0% in the High band against 4.6% in the Low band.        |
  |Most leavers are in the middle, not the extreme.| The Moderate burnout band holds 745 of the 895 leavers (83%); the High band holds only 87, from 161 employees.|
  |Weak manager support multiplies risk more than fourfold.| Attrition is 43.8% for Low support versus 10.1% for High.     |
  |Poor work-life balance is as damaging as burnout.| Poor band 52.7% (74 staff) versus High band 9.8% (1,177).            |
  |Disengaged employees almost always leave.| Low engagement 58.9% (56 staff), Moderate 23.7%, High 6.9%.                  |
  |Customer Support is the hotspot.| Its attrition is 24.1% against 17.9% company-wide. It holds 12.7% of staff but 17.1% of all exits (153). It also has the lowest average income (5,527 versus 6,991 company-wide) and the second-highest average burnout (4.37).|
  |Frontline support and sales-facing roles carry the highest rates.| Support Team Lead 26.0%, Support Specialist 25.2%, Content Strategist 23.9%, Account Executive 23.8%. The lowest are R&D Manager 10.0% and Software Engineer 12.3%.           |
  |In Customer Support, the drivers hit harder.| Low manager support 68.8% (16 staff), High overtime 72.7% (11), High burnout 61.3% (31), Poor work-life balance 56.2% (16), commute of 61-80 minutes 47.4% (38).                                |
  |Small raises and slow promotion push people out.| Low salary hike (up to 7.5%) 28.5% versus 11.4% for High. Staff 4 to 8 years since promotion leave at 26.6% versus 16.8% for the recently promoted.                                               |
  |The overall pay gap hides a level effect.| Stayers earn 7,060 on average against 6,675 for leavers, a 5.5% gap.         |

  ## RECOMMENDATIONS
  The actions below follow the insights and are ordered by expected impact.

-**Introduce an attrition early-warning flag:** Flag every employee whose burnout score is above 3.0 and whose manager support score is below 7.1. Give HR personnel a monthly list, grouped by department and manager, and ask them to hold a short stay conversation with each flagged employee.
- **Monthly wellbeing pulse:** Survey burnout, engagement and work-life balance monthly on the existing 1 to 10 scales; the Moderate burnout band holds 83% of leavers (745 of 895), so act before staff reach the High band.
- **Overtime guardrails:** Escalate anyone above 20 hours a week (52.4% attrition) and review workloads at 10 to 20 hours (28.7%), against 14.9% at 10 hours or fewer.
- **Manager capability programme:** Train managers in one-to-ones and workload conversations, and add the support score to their reviews; attrition is 43.8% under Low support against 10.1% under High.
- **Customer Support retention plan:** Add staffing buffers, a pay benchmark and a salary-hike floor; the department leaves at 24.1% against 17.9%, and reaching the company rate would avoid about 39 exits.
- **Redesign frontline support roles:** Set caseload limits, shift rotation and a defined career path for Support Team Lead (26.0% attrition) and Support Specialist (25.2%).
- **Protect work-life balance:** Offer flexible scheduling and no-meeting windows, starting with the Poor band (52.7% attrition, 74 staff).
- **Promotion review at four years:** Review the 540 staff past four years without promotion, who leave at 26.6% to 33.3% against 16.8% for the recently promoted.
- **Targeted equity or recognition pilot:** Pilot awards in Customer Support, where staff without stock options leave at 30.2% against 21% to 22% for those with options.
  
