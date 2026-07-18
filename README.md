# 🏥 Healthcare Workforce Risk Dashboard

**Excel · Pivot Tables · 464 hospitals · 2009–2013**

I saw a workforce planning job open up and wanted to show I actually know how to do this kind of work, not just talk about it. So I found a public hospital staffing dataset and tried to answer one question: can you spot, just from the numbers, when a hospital is about to have a staffing problem?

Turns out yes. And small rural hospitals losing their nurses to agency workers was the pattern that stood out the most.

-----

## 🗂️ The data

One CSV from Kaggle: California hospital staffing data, 2009 to 2013, from the state's official hospital report.

The file has 37,604 rows. But that's not 37,604 hospitals. Each hospital gets 17 rows per year, one for each type of staff (nurses, agency workers, management, cleaning staff, and so on). So one hospital, over 5 years, can easily be 85 rows.

Once you group all those rows back together by hospital and year, you get 2,207 records. One row per hospital per year, everything in one place. That's the table I actually worked from.

85 of the original rows (5 full hospital-years) had no hospital name or ID at all. Just numbers, no way to know whose they were. I couldn't guess, so I left them out.

-----

## 🧮 What I did before building the dashboard

Here's the part I almost got wrong.

Those 17 rows per hospital-year aren't 17 different things. 10 of them say who did the work (nurse, aide, agency worker). 7 say which department the hours were billed to (dietary, admin, etc). Same hours, just split two ways. If you add all 17 together, you count every hour twice without noticing.

I caught it by checking if both groupings added up to the same total per hospital. Most of the time they did, which is exactly why it's easy to miss. But in about 500 of the 2,207 hospital-years they didn't match. One case was off by over a million hours. Probably just bad reporting on one side. From then on, I only used the "who did the work" grouping.

After that, I built one row per hospital per year with a few things:

- How much of the staffing came from agency workers
- How much came from the hospital's own nurses vs. aides
- How the staffing level compares to similar hospitals (same type, same year)
- How much each of these changed from the year before
- Whether all three warning signs showed up at the same time

That last one is the "risk flag." I made it hard to trigger on purpose. A hospital only gets flagged if agency use went up, nurse share went down, AND staffing was below similar hospitals, all in the same year. Any one of those alone happens all the time and doesn't mean much. All three together is rare, and worth actually looking into.

-----

## 📊 The dashboard

One page. 4 numbers, 4 charts. You can get the whole picture in under a minute.

![Dashboard](dashboard-preview.png)

-----

## 🔢 The 4 numbers

Agency share, RN share, staffing level, and hospitals at risk, all for 2013, the last year in the data. These are just a snapshot. The trend is in the charts next to them.

-----

## 📈 Agency reliance over time

Agency use went down after 2010, then crept back up, ending 2013 almost where it started.

By itself this chart doesn't say much. It only matters next to the one below.

-----

## 📉 Registered nurse share over time

This is the chart that changes everything.

Nurse share stayed around 68% for 3 years, then dropped to 65.7% in 2013. That's the biggest single-year drop in the whole dataset. And it happened the same year agency use was climbing again.

-----

## 🚨 Hospitals flagged as at-risk, by year

36 hospital-years flagged in 2010, up to 64 by 2013. That's a 78% jump in 3 years. And it hit 147 different hospitals, not the same few over and over.

-----

## 🗺️ Top 10 counties by agency reliance

Every county at the top is small and rural: Colusa, Yuba, Modoc, Merced, Lassen.

The biggest single jump in the whole dataset belongs to a small rural hospital. It went from barely using agency staff to about 1 in every 6 hours worked being agency, in just one year. That's a hiring problem, not a money problem. It needs a different fix than a big city hospital would.

-----

## 💡 A few honest notes before you trust these numbers

**"Agency reliance" is a little broader than it sounds.**
It includes agency nurses, but the raw data also lumps in some non-nursing contract staff, like kitchen or cleaning workers. So read it as "how much this hospital depends on outside workers overall," not just agency nurses.

**Hospitals are only compared to similar hospitals.**
A small specialty hospital doesn't staff the same way as a big general hospital. Comparing every hospital to one flat average would flag the wrong ones. So each hospital is only compared to others of the same type (district, non-profit, city/county, investor-owned), in the same year.

**Staff titles don't translate the same everywhere.**
What this dataset calls "Aides & Orderlies" is what Ireland and the UK call a Healthcare Assistant (HCA). The US "LVN" title doesn't really have a match outside the US. Most other countries only have two nursing levels, RN and HCA, nothing in between.

**Why nurse share looks high here.**
This is hospital data, where nurses do most of the hands-on care themselves because of staffing ratio laws. That's different from nursing homes or disability care, where nurses mostly supervise and support staff do the daily care. The method still works there, watching the trend, flagging when it gets worse. The expected ratio just wouldn't be the same.

-----

## 🛠️ Tools I used

| | |
|---|---|
| Kaggle | Where the data came from |
| Excel · Pivot Tables | Grouping the raw rows into one row per hospital per year |
| Excel formulas | Shares, year-over-year change, comparing to similar hospitals, risk flag |
| GitHub | Sharing this project |

```
Agency Share = Agency Hours ÷ Total Hours Worked

RN Share = RN Hours ÷ (RN Hours + LVN Hours + Aide Hours)

Peer Benchmark = AVERAGEIFS(Total HPPD, Ownership Type, Year)
  average staffing level of hospitals with the same ownership type, same year

Agency Share Change = This Year's Agency Share minus Last Year's Agency Share

RN Share Change = This Year's RN Share minus Last Year's RN Share

Risk Flag = IF(
    Agency Share Change > 0
    AND RN Share Change < 0
    AND Total HPPD < Peer Benchmark,
  "At Risk", "")
```
