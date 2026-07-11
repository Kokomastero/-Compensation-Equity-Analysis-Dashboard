# 💰 Compensation Equity Analysis Dashboard
### *"Are We Paying Our People Fairly — And Are We Retaining The Right Talent?"*

> **Power BI | DAX | Excel | Python | HR Analytics | People Analytics | Compensation Analytics**

![Dashboard Preview]
<img width="1733" height="797" alt="Salary Budget" src="https://github.com/user-attachments/assets/c7ac3d5e-5c8e-4163-bd2b-568c09c72d24" />
<img width="1733" height="793" alt="Gender Pay Gap" src="https://github.com/user-attachments/assets/78a9b16a-6f85-4ddb-a002-5cca4d6df734" />
<img width="1735" height="802" alt="Flight Risk Analysis" src="https://github.com/user-attachments/assets/7af84bf7-b515-4711-852d-5e8de067bb00" />


---

## 📌 The Business Problem

Every Compensation & Benefits Manager walks into a salary review cycle asking the same question:

> *"Are we paying our people fairly — and are we keeping the right ones?"*

Most organisations cannot answer that question with data. Salaries are set at hire and never benchmarked again. Gender pay gaps go undetected until an audit. High performers leave quietly because nobody noticed they were underpaid — and nobody calculated what replacing them would cost.

This project was built to answer that question with data — analysing compensation structures, pay equity, performance alignment and flight risk across a 500-employee workforce to support three specific decisions a Compensation & Benefits Manager actually makes:

1. **Where should we allocate our salary review budget?**
2. **Do we have a gender pay gap that needs immediate action?**
3. **Which high performers are underpaid and about to walk out?**

---

## 📁 Repository Structure

```
compensation-equity-analysis/
│
├── 📁 dataset/
│   └── Compensation_Equity_Dataset.xlsx      # Synthetic dataset (500 records, 22 columns)
│
├── 📁 screenshots/
│   ├── Salary_Budget.png                     # Page 1 — Salary Budget Overview
│   ├── Gender_Pay_Gap.png                    # Page 2 — Gender Pay Gap Audit
│   └── Flight_Risk_Analysis.png              # Page 3 — Flight Risk & Performance vs Pay
│
├── 📁 pbix/
│   └── Compensation_Equity_Analysis.pbix     # Power BI project file
│
└── README.md
```

---

## 📂 About the Dataset

### Why Synthetic Data?

Employee compensation data is among the most sensitive information a company holds — salaries, performance ratings, satisfaction scores and flight risk flags are highly personal and confidential. Using synthetic data allows for realistic, meaningful analysis while fully respecting data privacy principles aligned with **NDPR (Nigeria Data Protection Regulation)** and **GDPR** best practices.

### How the Dataset Was Built

The dataset was generated using **Python (pandas, numpy)** with deliberate design decisions to reflect realistic Nigerian corporate compensation patterns:

**Salary generation logic:**
```python
# Base salary by job level
base_salary = {
    "Entry":   (1_200_000, 2_000_000),
    "Junior":  (2_000_000, 3_500_000),
    "Mid":     (3_500_000, 6_000_000),
    "Senior":  (6_000_000, 10_000_000),
    "Manager": (10_000_000, 18_000_000),
}

# Department multipliers (IT and Finance pay more)
dept_multiplier = {
    "IT": 1.30, "Finance": 1.20, "Sales": 1.10,
    "Customer Service": 0.85, "Admin": 0.80
}

# Gender pay gap built in (gets worse at senior levels)
gender_gap = {
    "Entry":   {"Male": 1.00, "Female": 0.98},
    "Junior":  {"Male": 1.00, "Female": 0.96},
    "Mid":     {"Male": 1.00, "Female": 0.92},
    "Senior":  {"Male": 1.00, "Female": 0.88},
    "Manager": {"Male": 1.00, "Female": 0.85},
}
```

The gender pay gap was deliberately built in and designed to worsen at senior levels — reflecting a real and well-documented pattern in corporate compensation. This was not a bug; it was the insight the analysis was designed to surface.

**Flight risk logic:**
```python
if perf_score >= 4 and comp_band == "Below Market":
    flight_risk = "High"
elif perf_score >= 4 and comp_band == "At Market":
    flight_risk = "Medium"
elif perf_score <= 2:
    flight_risk = "Low"
else:
    flight_risk = random.choices(["Low", "Medium", "High"],
                                  weights=[50, 35, 15])[0]
```

Note: Some Above Market employees are also flagged High Flight Risk — reflecting the reality that compensation alone does not drive retention. Low satisfaction scores, poor management and lack of career growth can cause even well-paid employees to leave.

---

## 📋 Column Dictionary — Every Column Explained

| Column | Description | Why It Matters |
|--------|-------------|----------------|
| **Employee ID** | Unique identifier (EMP-2001 format) | Tracks individuals without exposing personal names — data privacy best practice |
| **Department** | One of 9 departments | Enables department-level compensation benchmarking and equity analysis |
| **Job Role** | Specific role within department | Granular analysis of which roles have the widest pay gaps |
| **Job Level** | Entry / Junior / Mid / Senior / Manager | The most important variable — compensation should scale with level |
| **Gender** | Male / Female | Required for pay equity and gender gap calculations |
| **Location** | 6 Nigerian cities | Tests whether geography affects compensation |
| **Education Level** | OND / HND / Bachelor's / Master's / PhD | Explores whether education level correlates with pay outcomes |
| **Employment Type** | Full-time / Contract | Enables segmentation of permanent vs contract compensation |
| **Years of Experience** | Total years of relevant experience | Controls for experience when comparing salaries |
| **Tenure at Company (Yrs)** | Years at this specific company | Long tenure + below market pay = highest flight risk |
| **Annual Salary (₦)** | Total annual base salary in Naira | Primary compensation variable for all analysis |
| **Monthly Salary (₦)** | Annual salary ÷ 12 | More intuitive for Nigerian payroll context |
| **Bonus (₦)** | Performance-linked annual bonus | Higher performers receive higher bonus percentages (0–12% of salary) |
| **Total Compensation (₦)** | Annual Salary + Bonus | True cost of employee to organisation |
| **Market Rate Benchmark (₦)** | Mid-point of market salary range for this level and department | The standard against which all salaries are measured |
| **Pay vs Market (%)** | ((Annual Salary - Market Rate) / Market Rate) × 100 | Positive = above market. Negative = underpaid. The core equity metric |
| **Compensation Band** | Above Market / At Market / Below Market | Categorical version of Pay vs Market for filtering and charting |
| **Salary Band** | Entry Band / Junior Band / etc. | Groups employees by level for band-level analysis |
| **Performance Score** | 1 (Poor) to 5 (Excellent) | Measures output — should correlate with compensation in a fair system |
| **Performance Rating** | Poor / Below Average / Average / Good / Excellent | Text version of Performance Score for readability |
| **Satisfaction Score** | 1.0 to 5.0 | Employee engagement proxy — low scores signal flight risk regardless of salary |
| **Flight Risk** | High / Medium / Low | Composite flag combining performance, compensation band and satisfaction |

---

## 🧮 How Every KPI Was Calculated

### DAX Measures — Full Logic Explained

**Total Headcount**
```dax
Total Headcount = COUNTROWS('Compensation_Equity_Dataset')
```
*Counts every row in the table. Each row = one employee.*

---

**Average Male and Female Salary**
```dax
Avg Male Salary = 
CALCULATE(
    AVERAGE('Compensation_Equity_Dataset'[Annual Salary (₦)]),
    'Compensation_Equity_Dataset'[Gender] = "Male"
)
```
*CALCULATE changes the filter context — it averages salary but only for rows where Gender = Male. Same logic for Female.*

---

**Gender Pay Gap %**
```dax
Gender Pay Gap % = 
DIVIDE([Avg Male Salary] - [Avg Female Salary], [Avg Male Salary], 0)
```
*Standard methodology: gap expressed as a percentage of male salary. Positive = men earn more. The DIVIDE third argument (0) prevents division errors if no male salary data exists.*

---

**% Below Market**
```dax
Pct Below Market = 
DIVIDE(
    CALCULATE(
        COUNTROWS('Compensation_Equity_Dataset'),
        'Compensation_Equity_Dataset'[Compensation Band] = "Below Market"
    ),
    COUNTROWS('Compensation_Equity_Dataset'),
    0
)
```
*Numerator: count of Below Market employees. Denominator: total headcount. Result: percentage of workforce underpaid.*

---

**High Performers Below Market**
```dax
High Performers Below Market = 
CALCULATE(
    COUNTROWS('Compensation_Equity_Dataset'),
    'Compensation_Equity_Dataset'[Performance Score] >= 4,
    'Compensation_Equity_Dataset'[Compensation Band] = "Below Market"
)
```
*Performance Score >= 4 captures Good (4) and Excellent (5). Two conditions inside CALCULATE = AND logic. Both must be true simultaneously.*

---

**Estimated Replacement Cost**
```dax
Est Replacement Cost = 
SUMX(
    FILTER(
        'Compensation_Equity_Dataset',
        'Compensation_Equity_Dataset'[Flight Risk] = "High"
    ),
    'Compensation_Equity_Dataset'[Annual Salary (₦)] * 0.5
)
```
*Industry research places replacement cost at 50%–200% of annual salary. This model uses a conservative 50% of each individual's actual salary — making the estimate proportional to seniority. SUMX loops through every High Flight Risk employee and sums their individual replacement costs.*

---

**Potential Savings if Retained**
```dax
Potential Savings if Retained = 
[Est Replacement Cost] - 
SUMX(
    FILTER(
        'Compensation_Equity_Dataset',
        'Compensation_Equity_Dataset'[Flight Risk] = "High" &&
        'Compensation_Equity_Dataset'[Compensation Band] = "Below Market" &&
        'Compensation_Equity_Dataset'[Performance Score] >= 4
    ),
    'Compensation_Equity_Dataset'[Market Rate Benchmark (₦)] - 
    'Compensation_Equity_Dataset'[Annual Salary (₦)]
)
```
*Replacement cost minus total salary adjustment needed to bring underpaid high performers to market rate. If positive — retaining is cheaper than replacing. The && operator applies multiple AND conditions inside FILTER.*

---

**Departments with Gender Pay Gap > 10%**
```dax
Depts With Gap > 10% = 
COUNTX(
    VALUES('Compensation_Equity_Dataset'[Department]),
    IF(
        DIVIDE(
            CALCULATE(AVERAGE('Compensation_Equity_Dataset'[Annual Salary (₦)]),
            'Compensation_Equity_Dataset'[Gender] = "Male") -
            CALCULATE(AVERAGE('Compensation_Equity_Dataset'[Annual Salary (₦)]),
            'Compensation_Equity_Dataset'[Gender] = "Female"),
            CALCULATE(AVERAGE('Compensation_Equity_Dataset'[Annual Salary (₦)]),
            'Compensation_Equity_Dataset'[Gender] = "Male"),
            0
        ) > 0.10,
        1,
        BLANK()
    )
)
```
*COUNTX loops through each department, calculates that department's gender pay gap, checks if it exceeds 10%, and counts the departments where it does. Returns a whole number.*

---

**% Female in Senior+ Roles**
```dax
% Female Senior+ = 
DIVIDE(
    CALCULATE(
        COUNTROWS('Compensation_Equity_Dataset'),
        'Compensation_Equity_Dataset'[Gender] = "Female",
        'Compensation_Equity_Dataset'[Job Level] IN {"Senior", "Manager"}
    ),
    CALCULATE(
        COUNTROWS('Compensation_Equity_Dataset'),
        'Compensation_Equity_Dataset'[Job Level] IN {"Senior", "Manager"}
    ),
    0
)
```
*Numerator: female count at Senior/Manager level. Denominator: ALL employees at Senior/Manager level — not total company headcount. Answers "of all senior roles, what percentage are female?" not "what percentage of the whole company are senior females?"*

---

## 🎯 Why I Chose These KPIs

Every KPI in this dashboard was chosen to answer a specific question a Compensation & Benefits Manager actually asks — not because it was easy to calculate or visually impressive.

| KPI | Why I Chose It | What It Replaces |
|-----|---------------|------------------|
| **% Below Market** | The single most important compensation health metric — tells you immediately whether your salary structure is competitive | Raw headcount numbers which don't reveal equity problems |
| **Gender Pay Gap %** | The headline equity metric used in UK pay gap reporting and increasingly in Nigerian multinational audits — a single number that surfaces a systemic problem | Separate male/female salary averages which require mental calculation to compare |
| **Depts With Gap > 10%** | Translates an organisation-wide gap into actionable department-level decisions — you can't fix everywhere at once, you need to know where to start | An overall company gap % which masks where the real problems are hiding |
| **% Female in Senior+ Roles** | Answers whether women are reaching senior positions at all — because a pay gap at senior level is a different problem than a pay gap caused by few women reaching senior level | Simple headcount numbers which don't reveal the representation picture |
| **High Performers Below Market** | The most urgent retention metric — high performers being underpaid are your highest flight risk and your most expensive to replace | Generic below-market counts which don't distinguish between high and low performers |
| **Est. Replacement Cost** | Converts a people problem into a financial decision — CFOs and HR Directors respond to naira figures, not headcount numbers | Flight risk flags without financial consequence attached |
| **Potential Savings if Retained** | Creates the business case for acting now rather than waiting — shows that proactive salary adjustment is cheaper than reactive recruitment | Replacement cost alone which only shows the cost of inaction without the cost of the solution |
| **AVG Salary High Performers** | Tests whether the organisation is actually rewarding performance — if this number is lower than AVG Salary overall, the system is broken | Overall average salary which hides whether performance and pay are correlated |

---

## 📐 Assumptions Made

Every data analysis makes assumptions. Being transparent about them is what separates a credible analyst from one who just shows pretty charts.

**1. Replacement cost = 50% of annual salary**
I used 50% of each employee's individual annual salary as the replacement cost estimate. Industry research places this figure between 50% and 200% depending on seniority and role complexity. I chose 50% deliberately as a conservative estimate — this means the ₦325M figure is likely an underestimate, making the business case for retention even stronger than presented.

*In a real exercise:* replacement costs would be calculated per role type using actual recruitment agency fees, onboarding costs, time-to-productivity estimates and hiring manager hours. Replacing a Senior IT Manager costs vastly more than replacing an Entry-level Admin Officer.

**2. Performance Score >= 4 = High Performer**
I defined high performers as employees with a Performance Score of 4 (Good) or 5 (Excellent). This is a common threshold in performance management frameworks. In a real organisation this threshold would be defined by HR policy — some organisations use top quartile, others use a specific rating label.

**3. Market Rate Benchmark = midpoint of salary range per level and department**
Market rate benchmarks were calculated as the midpoint of estimated salary ranges by job level and department. In a real compensation exercise these would come from formal salary surveys — Mercer, Radford, Hay Group or local Nigerian HR benchmarking reports such as those published by Jobberman and HR consultancies.

**4. Gender pay gap methodology follows UK standard**
The gap was calculated as (Male Average - Female Average) / Male Average × 100. This is the methodology used in UK mandatory gender pay gap reporting. A positive percentage means men earn more. A negative percentage (as seen in Admin at -16.94%) means women earn more. Both directions indicate an equity issue requiring investigation.

**5. Flight risk is a composite proxy — not a prediction**
Flight Risk was calculated using three variables: performance score, compensation band and satisfaction score. This is a reasonable starting model but real flight risk prediction would incorporate additional variables — tenure, recent promotion history, manager relationship scores, absenteeism patterns and external market conditions. This model should trigger HR conversations, not replace them.

**6. Satisfaction score correlates loosely with compensation**
In the dataset generation, satisfaction scores were loosely correlated with performance scores and compensation competitiveness — higher paid and higher performing employees tend to show slightly higher satisfaction. This reflects general research findings but real satisfaction drivers are highly individual and organisation-specific.

---

## 💼 Business Value of This Dashboard

This dashboard was not built to show what happened. It was built to support three specific decisions a Compensation & Benefits Manager makes during a salary review cycle.

**Decision 1 — Where should we allocate our salary review budget?**

Without this dashboard: The manager distributes budget based on manager requests, tenure or gut feel — resulting in some overpaid employees receiving increases while underpaid high performers get nothing.

With this dashboard: Page 1 immediately shows that 35.2% of the workforce is below market rate and where budget concentration currently sits. The manager can allocate review budget to below-market employees first — targeting spend where it has the most equity and retention impact.

**Estimated value:** Prevents budget being wasted on employees already above market while underpaid high performers remain at flight risk.

---

**Decision 2 — Do we have a gender pay gap that needs action before our board meeting?**

Without this dashboard: The manager pulls an average male and female salary, sees 1.51% and concludes the gap is acceptable. The Operations 22.41% gap goes undetected.

With this dashboard: Page 2 shows the gap by department — revealing that 3 departments would fail a pay equity audit immediately. The manager walks into the board meeting with specific departments, specific gaps and a specific action plan rather than a reassuring overall average that masks the real problem.

**Estimated value:** Prevents reputational and legal risk from an undetected pay equity violation. As pay transparency requirements grow globally — including increased regulatory pressure in Nigerian multinationals — early detection is significantly cheaper than remediation after an audit failure.

---

**Decision 3 — Who do we give salary increases to first to prevent losing them?**

Without this dashboard: The manager doesn't know which employees are underpaid until they resign. Exit interviews reveal salary dissatisfaction after the person has already left and a ₦250,000–₦500,000 recruitment process has started.

With this dashboard: Page 3 provides a ranked table of High Flight Risk employees sorted by lowest satisfaction score — the manager can act on Monday morning with a specific list of employees to prioritise for off-cycle salary review. The ₦187M savings figure gives the CFO a financial reason to approve the budget.

**Estimated value:** ₦187M in potential savings versus ₦325M replacement cost — a ₦138M difference that represents the direct financial return of using this dashboard to make a proactive decision rather than a reactive one.

---

**Who this dashboard is built for:**

| Stakeholder | What They Get From This Dashboard |
|-------------|----------------------------------|
| **Compensation & Benefits Manager** | A ranked action list of who to prioritise for salary review and why |
| **HR Director** | A pay equity audit summary ready for board presentation |
| **CFO / Finance Director** | A financial business case — ₦187M savings vs ₦325M cost of inaction |
| **Department Heads** | Visibility into their own team's compensation health and flight risk |
| **CEO** | One number — replacing 138 people costs ₦325M. Fixing their salaries costs less. |

---

## 📊 Dashboard Pages

### Page 1 — Where Is Our Salary Budget Going — And Is It Justified?
<img width="1733" height="797" alt="Salary Budget" src="https://github.com/user-attachments/assets/ba88802c-be06-4ea9-968f-1477c0f35f19" />

**Decision:** Budget allocation and salary band structure

**KPIs:** Total Headcount (500) · Total Payroll (₦2BN) · AVG Salary (₦4.93M) · % Below Market (35.20%) · AVG Performance Score (3.66)

---

### Page 2 — Do We Have A Gender Pay Gap That Needs Immediate Action?
<img width="1733" height="793" alt="Gender Pay Gap" src="https://github.com/user-attachments/assets/c37c4879-ba5a-4e5e-9f79-e3c8b6803c04" />

**Decision:** Pay equity audit and compliance

**KPIs:** AVG Male Salary (₦4.96M) · AVG Female Salary (₦4.89M) · Overall Pay Gap % (1.51%) · % Female in Senior+ Roles (52.82%) · Depts With Gap > 10% (3)

---

### Page 3 — Which High Performers Are Underpaid And About To Walk Out?
<img width="1735" height="802" alt="Flight Risk Analysis" src="https://github.com/user-attachments/assets/d6664455-7580-4214-b89a-408ea2b62f34" />


**Decision:** Retention intervention and salary review prioritisation

**KPIs:** Total High Flight Risk (138) · High Performers Below Market (110) · AVG Salary High Performers (₦4.71M) · Est. Replacement Cost (₦325M) · Potential Savings if Retained (₦187M)

---

## 🔍 Key Insights

| # | Finding | Business Impact |
|---|---------|-----------------|
| 1 | **35% of workforce paid below market on a ₦2BN payroll** | Budget spent with no equity framework behind it |
| 2 | **Operations gender pay gap: 22.41%** | Pay equity audit would fail 3 departments immediately |
| 3 | **Women reach Senior level but earn less when they get there** | Promotion is happening — equal pay is not |
| 4 | **138 High Flight Risk — 110 are high performers** | The people most likely to leave are the least replaceable |
| 5 | **Below Average earns ₦5.4M vs Excellent at ₦5.1M** | Compensation structure rewarding mediocrity |
| 6 | **Replacing 138 employees costs ₦325M** | Proactive retention saves ₦187M |

---

## ✅ Recommendations

**1. Introduce formal salary bands per job level**
Stop letting payroll be driven by negotiation. A structured framework ensures equity within and across levels.

**2. Immediate pay equity audit in Operations, Marketing and Sales**
Close every gender pay gap exceeding 10% before the next board reporting cycle.

**3. Off-cycle salary review for 110 high performers below market**
Prioritise by lowest satisfaction score first — employees below 2.5 are actively at risk.

**4. Redesign annual review to link pay to performance**
A Below Average performer earning more than an Excellent one signals a broken system. Fix it in the next cycle.

**5. Address Above Market flight risk through engagement**
Some well-paid employees are still leaving. These need management intervention and career development — not salary increases.

---

## ⚠️ Limitations

**1. Synthetic data**
All 500 records were generated using Python. Findings are illustrative — not drawn from a real company's HRIS.

**2. Replacement cost is an estimate**
50% of annual salary is a conservative industry benchmark. Real replacement costs vary significantly by seniority, department and recruitment channel.

**3. Flight risk is a proxy not a prediction**
The Flight Risk flag combines performance, compensation and satisfaction data. It does not capture manager relationships, personal circumstances or competitor offers — all of which influence real attrition decisions.

**4. Binary gender classification**
The dataset uses Male/Female only. Real equity analysis should account for a broader spectrum where data is available.

**5. Market rate benchmarks are approximations**
Benchmarks were calculated as salary range midpoints. In a real exercise these would come from formal salary surveys (Mercer, Radford, Hay) or Nigerian HR benchmarking reports.

---

## 🛠️ Tools & Technologies

| Tool | Usage |
|------|-------|
| **Power BI Desktop** | 3-page dashboard, all visualisations and interactivity |
| **DAX** | 12 custom measures for KPIs, pay gap, flight risk and financial projections |
| **Microsoft Excel** | Dataset storage and initial review |
| **Python (pandas, numpy)** | Synthetic dataset generation |

---

## 🗺️ HR Analytics Portfolio Roadmap

| # | Project | Status | Business Focus |
|---|---------|--------|----------------|
| 1 | Employee Attrition & Performance | ✅ Complete | Why are people leaving? |
| 2 | Recruitment Funnel & Time-to-Hire | ✅ Complete | Where are we losing candidates? |
| 3 | Compensation Equity Analysis | ✅ Complete | Are we paying fairly? |
| 4 | Payroll Analytics & Compliance | 🔄 Coming Soon | Are deductions accurate and compliant? |
| 5 | Headcount Forecasting Model | 🔄 Coming Soon | How many people will we need? |

---

## 👤 About Me

**Uzomah Chinemerem** — HR Analytics professional building at the intersection of people data and business strategy. Currently completing NYSC service in an HR department while building a specialised HR Analytics portfolio combining Power BI dashboard development with HR domain expertise.

Actively pursuing:
- Google Data Analytics Professional Certificate
- Microsoft PL-300 Power BI Data Analyst Associate
- SQL for Data Analysis
- HR Analytics — Harnessing HR Data for Organisational Success (Alison)

📎 [Connect on LinkedIn](www.linkedin.com/in/uzomah-chinemerem-b4759b410)

---

*This project uses a synthetic dataset generated for portfolio and educational purposes. All employee records, salary figures and company references are fictional.*
