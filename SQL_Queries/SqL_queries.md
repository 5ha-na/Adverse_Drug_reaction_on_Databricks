```sql
--top KPIs
SELECT
  COUNT(DISTINCT(Medicinal_Product)) AS Total_Drugs,
  COUNT(DISTINCT(Adverse_Drug_Reaction_Term)) AS Unique_Drugs_Reaction,
  COUNT(CASE WHEN Is_Class_Warning = true then Is_Class_Warning end) / count(*) AS Class_Warning,
  COUNT(CASE WHEN Is_Post_Marketing_Surveillance = true then Is_Post_Marketing_Surveillance end) AS Post_Marketing_Surveillance
FROM adr_table
```

```sql
-- Evidence Source Distribution
SELECT
  CASE
    WHEN Is_Clinical_Trial = true AND Is_Post_Marketing_Surveillance = false THEN 'Clinical Trial'
    WHEN Is_Post_Marketing_Surveillance = true AND Is_Clinical_Trial = false THEN 'Post-Marketing'
    WHEN Is_Clinical_Trial = true AND Is_Post_Marketing_Surveillance = true THEN 'Both'
    ELSE 'Neither'
  END AS category,
  COUNT(*) AS Count,
  Count(*) / sum(count(*)) OVER () AS perc
FROM adr_table
GROUP BY category
```

```sql
--Top 10 Most Adverse Drug Reactions
SELECT
  Adverse_Drug_Reaction_Term,
  COUNT(Adverse_Drug_Reaction_Term) AS Count,
  COUNT(Adverse_Drug_Reaction_Term) / SUM(COUNT(*)) over () as perc
FROM adr_table
GROUP BY Adverse_Drug_Reaction_Term
ORDER BY Count DESC
LIMIT 10
```

```sql
-- High-Risk Medications: Very Common Class Warnings
SELECT
  t.Medicinal_Product,
  t.Adverse_Drug_Reaction_Term,
  mm.SOC_Short_Name,
  t.Is_Clinical_Trial,
  t.Is_Post_Marketing_Surveillance,
COUNT(distinct t.Adverse_Drug_Reaction_Term) AS unique_reaction
FROM adr_table t
JOIN meddra_soc_mapping mm
ON t.MedDRA_SOC_Code = mm.MedDRA_SOC_Code
WHERE t.Is_Class_Warning = true AND t.Frequency = 'Very Common'
GROUP BY t.Medicinal_Product, t.Adverse_Drug_Reaction_Term, mm.SOC_Short_Name, t.Is_Clinical_Trial, t.Is_Post_Marketing_Surveillance
```

```sql
-- Distribution of Adverse Reactions by System Organ Class (SOC)
SELECT
  mm.SOC_Short_Name AS System_Organ_Class,
  SUM(CASE at.Frequency
        WHEN 'Very Common' THEN 5
        WHEN 'Common' THEN 4
        WHEN 'Uncommon' THEN 3
        WHEN 'Rare' THEN 2
        WHEN 'Very Rare' THEN 1
        ELSE 0
      END) AS severity_weighted_score
FROM adr_table at
LEFT JOIN meddra_soc_mapping mm
ON at.MedDRA_SOC_Code = mm.MedDRA_SOC_Code
GROUP BY System_Organ_Class
ORDER BY severity_weighted_score DESC;
```

```sql
-- Gender-Specific Adverse Reactions
WITH gender_reactions AS (
  SELECT
    Gender,
    Adverse_Drug_Reaction_Term,
    COUNT(Adverse_Drug_Reaction_Term) AS count
  FROM adr_table
  WHERE Gender IN ('Women', 'Men')
  GROUP BY Gender, Adverse_Drug_Reaction_Term
),
totals AS (
  SELECT
    SUM(CASE WHEN Gender = 'Women' THEN count ELSE 0 END) AS total_women,
    SUM(CASE WHEN Gender = 'Men' THEN count ELSE 0 END) AS total_men
FROM gender_reactions
)

SELECT
  gr.Adverse_Drug_Reaction_Term,
  SUM(CASE WHEN gr.Gender = 'Women' THEN gr.count ELSE 0 END) AS women_count,
  SUM(CASE WHEN gr.Gender = 'Men' THEN gr.count ELSE 0 END) AS men_count,
  SUM(CASE WHEN gr.Gender = 'Women' THEN gr.count ELSE 0 END) / sum(count(*)) over () AS women_percentage,
  SUM(CASE WHEN gr.Gender = 'Men' THEN gr.count ELSE 0 END) / sum(count(*)) over () AS men_percentage
FROM gender_reactions gr
CROSS JOIN totals t
GROUP BY gr.Adverse_Drug_Reaction_Term, t.total_women, t.total_men
HAVING women_count > 1 OR men_count > 1
ORDER BY (women_count + men_count) DESC
LIMIT 15;
```

```sql
--Documented Adverse Reactions Across Children, Adults and Elderly
WITH Age_reaction AS (
  SELECT
    Adverse_Drug_Reaction_Term,
    Age_Group,
    COUNT(Adverse_Drug_Reaction_Term) AS count
  FROM adr_table
  GROUP BY Age_Group, Adverse_Drug_Reaction_Term
)

SELECT
  ar.adverse_drug_reaction_term,
  SUM(CASE WHEN ar.age_group = 'Adults' THEN count ELSE 0 END) AS Adults_count,
  SUM(CASE WHEN ar.age_group = 'Children' THEN count ELSE 0 END) AS Children_count,
  SUM(CASE WHEN ar.age_group = 'The Elderly' THEN count ELSE 0 END) AS Elderly_count
FROM Age_reaction ar
GROUP BY ar.adverse_drug_reaction_term
HAVING adults_count > 3 OR Children_count > 3 OR Elderly_count > 3
```

