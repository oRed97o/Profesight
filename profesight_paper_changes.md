# Profesight: paper changes (running list)

For the groupmate who edits the live paper. Each item gives the location and the exact text. Items marked **TO DRAFT** are waiting on something (results, decisions); they will be added here as exact text once ready. Last updated Nov 11, 2026 (Part IV experiment text in D2, Part V drafted in E).

---

## A. Name and identity

**A1. App name.** Replace every "AcadLens" with **Profesight** (cover page, headers, any sentence that names the app).
Tagline to use on the cover page: **To oversee, to foresee, for faculty**
Cover page: app logo (the navy graduation-cap logo), app name, tagline, names of the group members.

---

## B. Part II (KPI table, Warning Thresholds)

**B1. Warning Thresholds table, row "Grade submission delay", Trigger cell.**
Replace the current text with:
> Any grade sheet submitted after the deadline in the semester; overall Grade Submission Timeliness Rate below 85%

Reason: the dashboard raises an alert for any late grade sheet in the selected semester (no "two consecutive terms" rule).

**B2. Warning Thresholds table, add three rows** (the dashboard already uses these):

| Warning | Trigger |
|---|---|
| Low passing rate | Student passing rate below 75% for a section |
| Low assessment score | Average assessment score below 65% for a section |
| Declining SETE rating | SETE rating trend declines by 0.4 or more from the previous semester |

Keep the existing row for SETE below 3.5 for two consecutive semesters. It matches the dashboard.

**B3. KPI 2 (Student Passing Rate), definition or notes.** Add this sentence:
> In the demonstration dashboard, passing rate is weighted by the number of enrolled students. In the production Portal, students with INC, dropped, or withdrawn status are excluded from the computation.

**B4. KPI name.** Keep **SETE Rating Trend** in the paper (the dashboard shows "SETE rating trend", the same name in sentence case). No change needed, just keep it consistent everywhere.

---

## C. Part III (dashboard guide answers)

Make the answers match the final dashboard.

**C1. Where the answer describes how a user explores details** (the answer about interaction), replace with:
> Clicking a KPI card, a bar in the section chart, or a row in the Section risk list opens a Details panel on the right side of the screen. The panel has two views: By KPI, which lists every section for the chosen KPI with its value and status, and By section, which shows all measures for one section, including SETE ratings per criterion. Users can also filter by academic year, semester, subject, and section.

**C2. Where the answer describes alerts**, replace with:
> The alert panel at the top lists class-level issues, each tagged Grades or SETE: any grade sheet submitted late in the semester, overall grade submission timeliness below 85%, a section passing rate below 75%, an average assessment score below 65%, and SETE ratings below 3.5 for two consecutive semesters. When a section has fewer than five SETE respondents, its SETE results are hidden and the panel shows a notice instead.

**C3. Where the answer lists the KPI cards.** The cards are grouped: **Grades** (Grade Submission Timeliness, Student Passing Rate, Average Assessment Score) followed by **SETE** (Average SETE Rating, SETE Rating Trend). Each card shows its value, target, and a status label: Meets target (green), Below target (yellow), Needs action (red).

**C4. Add one short paragraph** describing the Section risk panel and the privacy strip (final wording in D below, and this sentence for the strip):
> A "Privacy by design" strip reminds users that alerts are class-level only, SETE results are hidden under five respondents, and only the faculty member's own classes are shown, with no student names.

**C5. Colors.** Status colors are green (meets target), yellow (below target), and red (at or below the warning level), always shown together with a text label.

---

## D. Part IV (analytics model)

Facts to use (decided):

- **Problem:** at midterm, estimate which sections may finish below 75% passing (the KPI 2 warning level), so faculty can schedule review sessions or tutoring early.
- **Type and technique:** predictive analytics, classification with logistic regression (decision tree as a comparison).
- **Inputs (4):** assessment average so far, previous semester passing rate of the same section, the section's SETE composite from the previous semester (only with 5 or more respondents; SETE is collected late in the term, so the current semester's rating would not exist at midterm), enrollment size. The dashboard has a switch that removes SETE from the estimate. Not used: the current passing rate (it is the outcome) and on-time grade submission (leakage).
- **Output:** estimated chance of finishing below 75% passing, shown as Low (below 35%), Moderate (35% to 59%), or High (60% or more), with the main reasons and a suggested next step.
- **Dashboard demo:** a fixed illustrative formula, not trained (only 16 section-semester rows). `z = -0.8 - 0.8*A - 0.5*P - 0.4*S + 0.1*E`, `p = 1 / (1 + e^-z)`, where A = (assessment average - 75) / 10, P = (previous passing rate - 85) / 10, S = (SETE composite - 4.0) / 0.5 (0 if hidden), E = (enrolled - 35) / 10.
- **Seven-step workflow:** define the problem, collect data (Portal plus SETE; integration is proposed), clean and prepare (exclude INC, dropped, withdrawn, and sections under the respondent minimum), explore and select features (check for leakage), select the technique and split by time, train, evaluate.
- **Metrics:** recall on the at-risk class (missing a struggling class costs more than a false alarm), precision, confusion matrix, F1 if one number is needed.
- **Proof-of-concept experiment:** trained on the public UCI Student Performance dataset (student-level, secondary school, different from the Portal data). Results and exact text are in D2 below (script: `profesight_risk_experiment.py`). Must say clearly that this shows the method is feasible and measurable, not that the model is validated for the university.
- **Bias note (required):** SETE ratings reflect grading leniency, popularity, class size, and whether a course is required or elective; small samples are noisy. The estimate is a support signal only, never an evaluation of the teacher, and any decision needs human review by the Program Chair or Dean.
- **Wording rule:** call it an estimate and an early warning, not a prediction promise.

**D2. Part IV, Step 7 (Evaluate), proof-of-concept experiment.** Add these paragraphs as the evaluation text, and insert `confusion_matrix.png` under the second paragraph with the caption: *Confusion matrix on the held-out test set (99 students), logistic regression, 35% cutoff.*

> **Proof-of-concept experiment.** To check that the approach can be measured, we trained a logistic regression on the public UCI Student Performance dataset (Cortez and Silva), which contains 395 Mathematics students and 649 Portuguese students from secondary schools. A student was labeled at risk when the final grade was below 10 out of 20. The inputs were the first-period grade (G1), the second-period grade (G2), past failures, absences, and weekly study time. Demographic and family variables were left out on purpose, for fairness. This dataset is student-level and comes from a different school system, so the experiment shows that the method is feasible and measurable. It does not validate the model for the university.

> **Results.** On a held-out test set of 99 students (33 truly at risk), the model flagged 30 of the 33 at-risk students at a 50% cutoff (recall 0.91, precision 0.77) and all 33 at a 35% cutoff (recall 1.00, precision 0.75), with 11 false alarms. Five-fold cross-validation on all 395 students gave similar results: recall 0.92 and precision 0.78 at 50%, recall 0.98 and precision 0.74 at 35%, and a ROC AUC of 0.97. Lowering the cutoff caught more at-risk students at the cost of more false alarms, which is the trade-off behind the Moderate level in the dashboard. Recall matters most here because missing a struggling class costs more than a false alarm, while precision keeps alerts trustworthy so faculty do not start ignoring them.

> **Comparison with a simple rule.** The full model did not beat a simple rule. Flagging every student whose second-period grade was below 10 reached recall 0.91 and precision 0.83 on the same test set, so the logistic regression added little beyond G2, which is strongly related to the final grade. The more useful test is the earlier-warning version, which leaves G2 out and uses only the first-period grade and the other inputs. It reached recall 0.85 and precision 0.69 at a 50% cutoff, and recall 0.95 and precision 0.65 at 35% (ROC AUC 0.92), so it still flags most at-risk students earlier in the term, with more false alarms.

> **Generalization and section-level view.** A model trained on Mathematics and tested on Portuguese, a different course it had never seen, reached recall 0.86 and precision 0.64 at a 50% cutoff (recall 0.92 and precision 0.54 at 35%). It keeps most of its ability to catch at-risk students but produces more false alarms in a new setting. When students were shuffled into random groups of about 30 to imitate sections, the estimated at-risk share differed from the actual share by 6.5 percentage points on average, compared with 9.9 when always guessing the overall rate. The estimates also ran about five points higher than the actual rate on average (about 38% against 33%), because the model was set to favor catching at-risk students. The groups were random, not real classes, so this only shows that student-level estimates add up to a reasonable class-level number. It does not validate section-level prediction, and the percentage should be read as an estimate for ranking concern, not as an exact probability.

> **Limits.** The test set is small (99 students), so the 100% recall at the 35% cutoff should not be read as a guarantee; cross-validation gives 98%. The dataset is not from the university, and its at-risk definition (final grade below 10 out of 20) differs from the Portal's 75% passing line. The experiment did not include SETE ratings or class size, so those two inputs of the dashboard remain design assumptions that would have to be tested on real data.

**D3. Part IV, where the Section risk card is described (or the paragraph about the dashboard demo).** Add:

> The Section risk card in the dashboard does not use the model trained above. With only 16 section-semester rows, a model cannot be trained responsibly, so the card uses fixed, illustrative coefficients chosen by hand to show how the output would look. In production, the model would be trained on earlier semesters of Portal and SETE data, tested on the most recent semester (a time-based split), checked for recall and precision, calibrated against real outcomes, and monitored every semester.

**D4. Part IV, SETE bias note.** Add at the end:

> To limit this risk, the model uses the previous semester's SETE, because the current semester's ratings are not available at midterm. SETE counts only when a section has five or more respondents and carries about half the weight of the assessment average. The dashboard also lets the user switch SETE off, which shows how much it changes the estimate (for example, from 65% High to 55% Moderate for one section). When every other input is on target, a below-target SETE rating alone can move a section to Moderate, and to High only if the rating is about 2.5 out of 5 or lower.

**D5. Part IV, where the output is described.** Add:

> The percentage is an estimate from a simple model that has not been calibrated against real outcomes. It is meant to rank sections by level of concern, not to state an exact probability.

---

## E. Part V (System Evaluation and Recommendations)

**E1. Location:** after Part IV, under the heading "Part V. System Evaluation and Recommendations (10 Points)". Add one answer for each guide question, using the exact text below.

**Guide question 1: What three types of testing should be conducted before implementation?**

> Before implementation, Profesight should go through three types of testing.
>
> **1. Functional and data accuracy testing.** Check that each KPI is computed correctly by comparing dashboard values with the Portal and SETE source records for sample classes, and that the filters, alerts, drill-down, and the five-respondent rule behave as designed. This includes confirming that INC, dropped, and withdrawn students are excluded from the passing rate and that late grade sheets raise alerts.
>
> **2. Model validation and fairness testing.** Test the Section risk estimate with a time-based split: train on earlier semesters and test on the most recent one. Report recall and precision, check how well the percentages match real outcomes, and compare results across subjects and class sizes. Also test how much the estimates change when SETE is switched off.
>
> **3. Security and privacy testing.** Verify login and role-based access (a faculty member sees only their own classes), encryption, audit logs of who viewed what, and that no student names or small-group SETE results can appear. This testing should also check compliance with the Data Privacy Act of 2012 (RA 10173) and university data policies.
>
> After these tests, a short pilot with a few faculty members and program chairs should confirm that the dashboard is understandable and useful before wider rollout.

**Guide question 2: What privacy, confidentiality, or fairness concerns must be addressed?**

> **Privacy and confidentiality.** Student grades and SETE responses are personal data under RA 10173, so access must be limited to what each role needs: faculty see only their own classes, and program chairs and deans see only what their duties require. The dashboard shows class-level results and never shows student names. SETE results are hidden when a section has fewer than five respondents so that individual students cannot be identified, and any SETE comments added in the future would need the same protection. Data should be encrypted, every view should be logged, and retention and sharing rules should be documented.
>
> **Fairness.** SETE ratings can be affected by grading leniency, popularity, class size, and whether a course is required or elective, and small samples are noisy, so they should not be treated as a pure measure of teaching quality. The Section risk model should not use demographic information about students or faculty, and its results should be checked for uneven performance across subjects and class sizes. The estimate is a support signal for early help, not an evaluation of the teacher.

**Guide question 3: How can the university ensure that SETE results are interpreted appropriately?**

> The university can support appropriate interpretation in five ways. First, always show SETE with context: the number of respondents, the course type, and the class size, and hide results with fewer than five respondents. Second, emphasize trends across several semesters instead of a single rating, which is why the dashboard shows the SETE Rating Trend and raises an alert only when ratings stay below 3.5 for two consecutive semesters. Third, read SETE together with other evidence, such as passing rates, assessment scores, and classroom or peer observation, instead of alone. Fourth, give users short guidance and training, including notes on the dashboard, that explain what SETE does and does not measure. Fifth, require human review: a low rating should start a supportive conversation with the Program Chair, with the faculty member able to add context, and should never trigger an automatic personnel decision.

**Guide question 4: Recommend two specific ways the university could use the proposed analytics solution.**

> **1. Midterm academic support program.** Use the Section risk estimate to identify sections that may finish below 75% passing, so the Program Chair can schedule review sessions, assign peer tutors, or refer struggling students for tutoring while there is still time to help. Class-level summaries can also show where tutoring resources are needed most each semester.
>
> **2. Targeted faculty development.** Use the SETE ratings per criterion and the trend to see which teaching area needs support, such as Student Engagement, which was the lowest-rated criterion in the sample data. The university can then offer coaching, peer observation, or short workshops on that area and share effective practices from sections with high ratings. Participation should be supportive and voluntary, not punitive.

---

## F. Notes on the SETE trend (add where SETE interpretation is discussed)

> When a section's SETE results are hidden because it has fewer than five respondents, the SETE Rating Trend compares different sets of sections between semesters, so it should be read together with the number of respondents shown on the card.

---

## G. Submission checklist

- Cover page: logo, Profesight, tagline, group member names
- Printed document on long bond paper
- One colored dashboard page (screenshot of the dashboard)
- Dashboard link (the final file will be published at the artifact link)
- Submission date: Nov 23
