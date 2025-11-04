<p align="center">
  <img src="who-logo.png" alt="WHO logo" width="200" />
</p>

# eHealth4EveryOne

This repository contains the WHO Performance Dashboard (Power BI and Tableau assets), sample data used for the dashboard developer assignment, and supporting documentation. The dashboard was built to satisfy the assignment brief in `ASSIGNMENT- DASHBOARD DEVELOPER.docx` and to answer the analysis questions in that document.

**Main goals**

* Build an interactive dashboard that compares health indicators, shows geographic distribution, trends over time, and disaggregation by age/gender.
* Use DAX (Power BI) or calculated fields (Tableau) to perform simple analytical calculations.
* Produce a short user manual (slide deck) and provide interpretations for the results.

---

## Repository contents

* `WHO Performance Dashboard.pbix` — Power BI Desktop file (interactive report).
* `WHO Performance Dashboard.twb` — Tableau workbook.
* `Sample Data - Dashboard Developer.xlsx` / `Sample Data.csv` — Example dataset used to build the dashboard.
* `ASSIGNMENT- DASHBOARD DEVELOPER.docx` — Assignment brief and list of questions (used to drive the build).
* `WHO Performance Dashboard User Manual.pptx` — Short slide deck with chart descriptions, filters and interpretations.
* `Excel_Assignment (1).xlsx` — Supporting workbook created for the assignment.

---

## Data source

Primary sample data used for the dashboard is in this repo (`Sample Data - Dashboard Developer.xlsx`). The original data reference provided in the assignment brief is a Google Sheets link — you can use either dataset to refresh the report.

---

## What the dashboard includes (mapping to assignment questions)

**Question 1 requirements implemented:**

1. Comparison between indicators: a central comparison visual (clustered bar / small multiples) allowing selection of indicator, period and source.
2. Map: a choropleth map of Nigerian states showing values for the selected indicator, gender, and source. State boundaries are joined using the `State` field in the dataset.
3. Trend analysis: line chart(s) showing indicator values across periods with filter for indicator and source.
4. Disaggregation view: visuals for disaggregation by **age** and **gender** that allow within-disaggregation comparison (stacked bars or small multiples).
5. Analytical calculations: DAX measures (Power BI) and calculated fields (Tableau) used to create derived metrics and aggregates.
6. User manual (PowerPoint, ≤7 slides): includes chart titles, filters/slicers used, step-by-step instructions for interacting with each visual, and short interpretations/insights.

**Question 2 (analysis) — approach included in the deliverable**

* Definitions and short conceptual notes are added to the user manual.
* For the analytical questions (top 10 states, time of noticeable HIV increase, male:female ratio, condom usage extremes), the PBIX contains the necessary measures and a set of pre-built report pages with pre-applied filters to answer each question. The slide deck includes the computed answers and brief interpretations.

---

## How to open and use the Power BI dashboard (quick start)

1. Clone the repository:

```bash
git clone https://github.com/George-sai/eHealth4EveryOne.git
cd eHealth4EveryOne
```

2. Open `WHO Performance Dashboard.pbix` in Power BI Desktop (Windows). If Power BI asks for data sources, point it to `Sample Data - Dashboard Developer.xlsx`.

3. Recommended workflow in Power BI Desktop:

   * Refresh data (`Home` → `Refresh`).
   * Use the **Indicator** slicer to pick which indicator to view.
   * Use the **Source** and **Sex** slicers to filter map and charts.
   * Use **Period** (year) to view trends.

4. To publish: `Home` → `Publish` (requires Power BI Service login). Copy the published report link for sharing.

---

## How to open and use the Tableau workbook

1. Open `WHO Performance Dashboard.twb` in Tableau Desktop or Tableau Reader.
2. If the data connection is broken, point the workbook to `Sample Data - Dashboard Developer.xlsx` or `Sample Data.csv` and refresh.
3. Use the dashboard filters (Indicator, Period, Sex, Source) to explore the views.

---

## Reproducible analysis — key DAX examples (Power BI)

Below are example measures used in the PBIX. Copy them into your model as needed and adapt field/table names to match your dataset.

**TotalValue (sum of indicator values)**

```dax
TotalValue = SUM('Data'[Value])
```

**Previous period value**

```dax
PreviousPeriodValue = CALCULATE([TotalValue], PARALLELPERIOD('Date'[Date], -1, YEAR))
```

**Year-over-Year change (percent)**

```dax
YoY_Change = DIVIDE([TotalValue] - [PreviousPeriodValue], [PreviousPeriodValue])
```

**Top 10 states table (used inside a table visual)**

```dax
Top10States =
TOPN(
    10,
    SUMMARIZE(
        'Data',
        'Data'[State],
        "StateValue", SUM('Data'[Value])
    ),
    [StateValue],
    DESC
)
```

**Male : Female tested-and-aware ratio** (example assuming there is a `TestedAndAware` numeric column and a `Sex` column):

```dax
MaleTestedAware = CALCULATE(SUM('Data'[TestedAndAware]), 'Data'[Sex] = "Male")
FemaleTestedAware = CALCULATE(SUM('Data'[TestedAndAware]), 'Data'[Sex] = "Female")
MaleFemaleRatio = DIVIDE([MaleTestedAware], [FemaleTestedAware])
```

**Condom usage extremes (rank states by condom usage)**

```dax
CondomUsageByState =
SUMMARIZE('Data', 'Data'[State], "CondomUsage", AVERAGE('Data'[CondomUsagePercent]))

HighestCondomUsage = TOPN(1, CondomUsageByState, [CondomUsage], DESC)
LowestCondomUsage = TOPN(1, CondomUsageByState, [CondomUsage], ASC)
```

*Adapt column names to match your dataset.*

---

## Calculated fields examples (Tableau)

Tableau calculated field snippets you can use in the workbook:

* **TotalValue**: `SUM([Value])`
* **Previous Period Value**: `LOOKUP(SUM([Value]), -1)` (or use `DATEADD` for year shifts)
* **YoY Change**: `(SUM([Value]) - LOOKUP(SUM([Value]), -1)) / ABS(LOOKUP(SUM([Value]), -1))`

---

## User manual (slide deck) — what's inside

The included `WHO Performance Dashboard User Manual.pptx` follows the assignment requirements and contains:

1. Slide 1 — Title and dataset description (fields explained).
2. Slide 2 — Chart list and where to find filters/slicers.
3. Slide 3 — Map page instructions (how to use indicator, sex, and source filters).
4. Slide 4 — Trend analysis page and how to change time granularity.
5. Slide 5 — Disaggregation visuals (age and gender) and interpretation guidance.
6. Slide 6 — DAX / calculated field snippets used (so reviewers can verify the logic).
7. Slide 7 — Answers to Question 2 with short interpretations (top states lists, period of HIV increase, male:female ratio method, condom usage extremes) and the publish link.

---

## Answers to Question 2 — how they are provided

The repository includes a slide in the user manual that contains the final answers to Question 2 (top-10 lists, time period with notable HIV increase, ratio of tested-and-aware, and condom usage extremes). If you want the raw tables exported or a CSV with the ranked state lists, run the following process in Power BI Desktop:

1. Create a table visual with `State` and the measure you want (e.g., `StateValue`).
2. Sort the visual by `StateValue` descending to get the top states.
3. Export the table to CSV from the visual (`...` → `Export data`).

This will produce the exact lists used in the slide deck.

---

## Design notes & best practices followed

* Keep visuals simple and avoid overcrowding: each page focuses on a single analytical story.
* Use slicers/filters to avoid over-aggregation.
* Validate date formats and nulls before refreshing.
* Use tooltips (Power BI) or tooltip sheets (Tableau) to provide definitions and metadata for indicators.

---

## How to publish and share

* **Power BI Service**: publish from Power BI Desktop. Share workspace/report link and, if required, create a dashboard tile for quick access.
* **Tableau Public / Server**: publish the workbook to Tableau Server or Tableau Public and include the published link in the slide deck.

Place the published link in the slide deck and in the top-level README so reviewers can access the live report.

---

## Contributing

If you want to improve the dashboard or adjust the data model:

1. Fork the repo.
2. Create a branch and commit your changes.
3. Update the `WHO Performance Dashboard User Manual.pptx` to reflect your changes.
4. Open a pull request describing your changes and why they improve the dashboard.

---

## Contact

If you want me to adapt this README to a shorter technical summary, a LinkedIn-friendly blurb, or to produce the CSV exports and answers to Question 2 directly from the sample data, tell me which format you want and I will add them to the repo.
