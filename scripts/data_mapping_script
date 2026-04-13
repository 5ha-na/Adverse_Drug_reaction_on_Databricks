```python
from pyspark.sql.types import IntegerType, StringType, StructType, StructField

# 1. Define the data
soc_data = [
    (10005329, "Blood and lymphatic system disorders", "Blood"),
    (10007541, "Cardiac disorders", "Cardiac"),
    (10010331, "Congenital, familial and genetic disorders", "Genetic/Congenital"),
    (10013993, "Ear and labyrinth disorders", "Ear"),
    (10014698, "Endocrine disorders", "Endocrine"),
    (10015919, "Eye disorders", "Eye"),
    (10018065, "Gastrointestinal disorders", "Gastrointestinal"),
    (10017947, "General disorders and administration site conditions", "General/Admin"),
    (10021428, "Hepatobiliary disorders", "Hepatobiliary"),
    (10021881, "Infections and infestations", "Infections"),
    (10022117, "Injury, poisoning and procedural complications", "Injury/Poisoning"),
    (10022891, "Investigations", "Investigations"),
    (10027433, "Metabolism and nutrition disorders", "Metabolism"),
    (10028395, "Musculoskeletal and connective tissue disorders", "Musculoskeletal"),
    (10029104, "Neoplasms benign, malignant and unspecified", "Neoplasms"),
    (10029205, "Nervous system disorders", "Nervous System"),
    (10036585, "Pregnancy, puerperium and perinatal conditions", "Pregnancy"),
    (10077536, "Product issues", "Product Issues"),
    (10037175, "Psychiatric disorders", "Psychiatric"),
    (10038359, "Renal and urinary disorders", "Renal/Urinary"),
    (10038604, "Reproductive system and breast disorders", "Reproductive"),
    (10038738, "Respiratory, thoracic and mediastinal disorders", "Respiratory"),
    (10040785, "Skin and subcutaneous tissue disorders", "Skin"),
    (10041244, "Social circumstances", "Social"),
    (10042613, "Surgical and medical procedures", "Surgical"),
    (10047065, "Vascular disorders", "Vascular"),
    (10021879, "Immune system disorders", "Immune System")
]

# 2. Define explicit schema to FORCE IntegerType
schema = StructType([
    StructField("MedDRA_SOC_Code", IntegerType(), False),
    StructField("Official_SOC_Term", StringType(), True),
    StructField("SOC_Short_Name", StringType(), True)
])

# 3. Create and Save
soc_mapping_df = spark.createDataFrame(soc_data, schema=schema)

soc_mapping_df.write.mode("overwrite").saveAsTable("adr_data.adverse_drug_and_product_reaction.meddra_soc_mapping")
```
