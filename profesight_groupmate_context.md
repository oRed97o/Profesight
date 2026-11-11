# Profesight: context for the paper (for the groupmate)

You will edit the written paper. The dashboard is finished and verified, so this document tells you what it does, what the paper must say about it, and exactly which edits to make. If you use an AI assistant, paste this whole document into it together with `profesight_paper_changes.md`. Last updated Nov 11, 2026.

## The assignment
- Course: BAT 405 (Analytics Application), integrated case study "Enhancing the University Student and Faculty Portal Through Analytics and Decision Support". Group of 2. Programming is not required, but a working dashboard link is submitted.
- Parts: **II** KPI table, **III** dashboard design guide and wireframe or mockup (with a link), **IV** analytics model (25 points), **V** system evaluation and recommendations (10 points). The full instructions are in `BAT_405_Case_Study.docx`.
- Submission **Nov 23**: printed document plus the dashboard link. Cover page with the app logo, app name and names. Long bond paper. One colored page showing the dashboard.
- Our primary user is the **Faculty Member** (the sample user is Prof. Maria L. Santos).

## The app in one paragraph
**Profesight** ("To oversee, to foresee, for faculty") is a one-page dashboard for faculty members. It shows five KPIs from the Portal grade data and SETE (Student Evaluation of Teaching Effectiveness), an alert panel, charts, a drill-down panel, and an early-warning estimate called Section risk. It never shows student names, and it hides SETE results for sections with fewer than 5 respondents. The earlier name "AcadLens" is retired: replace it everywhere in the paper.

## What the final dashboard does (use this to keep Part III accurate)
- **Filters:** academic year, semester, subject, section. Everything updates to match.
- **Alerts panel (top):** each alert is tagged Grades or SETE. The alerts are: any grade sheet submitted late in the semester; overall grade submission timeliness below 85% (only when more than one section is selected); a section passing rate below 75%; an average assessment score below 65%; SETE below 3.5 for two consecutive semesters; and a notice when a section's SETE is hidden for having fewer than 5 respondents.
- **Five KPI cards**, grouped: **Grades** (Grade Submission Timeliness, Student Passing Rate, Average Assessment Score), then **SETE** (Average SETE Rating, SETE Rating Trend). Each card shows the value, the target, and a status label with color: Meets target (green), Below target (yellow), Needs action (red).
- **Three charts:** passing rate and assessment score by section; SETE composite rating by semester (with the 4.0 target and 3.5 warning lines); SETE rating by criterion (current semester compared with the previous one).
- **Details panel (right-side drawer):** opened by clicking a KPI card, a chart bar, or a Section risk row. It has two views: **By KPI** (every section for the chosen KPI, with value and status) and **By section** (all measures for one section, including SETE per criterion).
- **Section risk card:** estimates, for each section, the chance of finishing below 75% passing. Shown as Low (below 35%), Moderate (35% to 59%) or High (60% or more), with the main reasons and a suggested action. A switch lets the user turn SETE off in the estimate. A "How this estimate works" note explains the method.
- **Privacy by design strip:** class-level alerts only; SETE hidden under 5 respondents; your classes only, no student names.
- **Landing page:** explains how the dashboard works (four steps and a status legend), shows the Grades and SETE measures, and shows Section risk.

## Thresholds (target / warning level)
| KPI | Target | Warning level |
|---|---|---|
| Grade Submission Timeliness | 95% | 85% |
| Student Passing Rate | 85% | 75% |
| Average Assessment Score | 75% | 65% |
| Average SETE Rating | 4.0 | 3.5 |
| SETE Rating Trend | no decline over 0.2 | decline of 0.4 |

Status rule: green at or above the target; yellow between the target and the warning level; red at or below the warning level.

Assessment score = the simple average of the activities average and the exams average (equal weights, a demo simplification; a real version would use each course's grading weights).

## Sample data (all numbers are made up for the demo)
Four sections: IT 301 BSIT-3A, IT 301 BSIT-3B, IT 305 BSIT-2A, IT 312 BSIT-4A; four semesters (1st Sem 2024-25 to 2nd Sem 2025-26). For **2nd Sem 2025-26, all sections**: grade timeliness 50% (2 of 4 sheets on time), passing rate 81.4%, assessment score 77.0%, SETE rating 4.06 out of 5 (based on 90 of 143 students), SETE trend +0.04. IT 312 has only 4 SETE respondents, so its SETE is hidden. The lowest-rated SETE criterion is Student engagement (3.86).

Section risk for 2nd Sem 2025-26 with SETE on: BSIT-3B High 65%, BSIT-4A Moderate 35%, BSIT-3A Low 20%, BSIT-2A Low 10%. With SETE off: 55% Moderate, 40% Moderate, 20% Low, 15% Low.

## Part IV in short (the model)
- **Problem and decision:** at midterm, estimate which sections may finish below 75% passing, so faculty can schedule review sessions or tutoring early.
- **Analytics type and technique:** predictive analytics; classification with logistic regression.
- **Inputs (4):** assessment average so far; previous semester passing rate of the same section; the section's SETE composite from the previous semester (only with 5 or more respondents); enrollment size. **Output:** estimated chance of finishing below 75% passing (Low, Moderate, High).
- **Not used as inputs:** the current passing rate (it is the outcome) and on-time grade submission (leakage).
- **Metrics:** recall on the at-risk class (missing a struggling class costs more than a false alarm) and precision (keeps alerts trustworthy). Also a confusion matrix.
- **Required bias note:** SETE reflects grading leniency, popularity, class size, and required versus elective courses; results must never automatically decide personnel matters.
- **The dashboard card is illustrative.** Its weights are fixed and hand-chosen, because 16 rows of sample data cannot train a model. A separate proof-of-concept experiment trained a real logistic regression on the public UCI Student Performance dataset (results are in section D2 of the changes list, with the picture `confusion_matrix.png`). It shows the method is measurable. It does not validate the model for the university.
- **Wording rule:** call the output an "estimate" and an "early warning", never a "prediction" that is guaranteed. Say "not a calibrated probability".

## Terms to keep consistent everywhere
| Use | Do not use |
|---|---|
| Profesight | AcadLens |
| SETE Rating Trend (paper), SETE rating trend (dashboard) | SETE trend |
| Section risk | risk score, prediction |
| estimate, early warning | prediction promise |
| Meets target / Below target / Needs action | pass / fail |
| Faculty Member (primary user) | teacher evaluation tool |

## Your editing checklist (in order)
1. Open `profesight_paper_changes.md`. It lists every edit with its **location** and the **exact text** to paste. Work through sections A to G.
2. **A:** rename AcadLens to Profesight everywhere, and build the cover page (logo file `profesight_logo.png`, app name, tagline, both names).
3. **B (Part II):** change the grade-submission warning row, add three warning rows, and add the passing-rate weighting note.
4. **C (Part III):** replace the answers about exploring details, alerts, the KPI cards, the privacy strip and colors, so they match the final dashboard described above.
5. **D (Part IV):** add the experiment text (D2) with `confusion_matrix.png`, the demo paragraph (D3), the SETE bias addition (D4) and the estimate wording (D5). **Check first** whether Part IV tasks 1 to 6 (problem, analytics type, technique, inputs and output, seven workflow steps, two metrics) are already answered in the live paper. The list's section D gives the facts to use but not a full write-up of those six tasks. If they are missing, ask for a draft before you finish.
6. **E (Part V):** add the four answers under the Part V heading.
7. **F:** add the SETE trend note where SETE interpretation is discussed.
8. Check that the **KPI 4 definition** says "average of the activities and exams scores", and that every threshold in the paper matches the table above.
9. Do not change any number from the sample data, and do not change the dashboard file.

## Still pending (not your job, but you need them for the final paper)
- The one-page colored **screenshot** of the dashboard.
- The **dashboard link** (the final file will be published to the existing artifact link; the "Link:" line in Part III and the submission need it).
- The final read-through and printing on long bond paper.
