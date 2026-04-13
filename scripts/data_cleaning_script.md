```Python
import pyspark.sql.functions as F

#Loading Raw Data: 
adr_df = spark.table("adverse_reaction_with_preferred_term_and_system_organ_class_codes")

# Cleaning Frequency, Gender and Age_Group columns:
adr_df = adr_df\
.withColumn("Frequency", 
            F.initcap(F.when(F.col("Frequency") == "Unknown, not mentioned or no standard category", "Unknown")
            .otherwise(F.col("Frequency"))))\
.withColumn("Gender", 
            F.initcap(F.when(F.col("Gender").contains("Not specified"), "Not Specified")
            .otherwise(F.regexp_replace(F.col("Gender"), "^ADR occurs in | only$", ""))))\
.withColumn("Age_Group", 
            F.initcap(F.when(F.col("Age_Group").contains("Age category not"), "Not Specified")
            .otherwise(F.regexp_replace(F.regexp_replace(F.col("Age_Group"), "^ADR occurs in |^ADR occurring in ", ""), " only$", ""))))\
.withColumn("Causality", 
            F.initcap((F.regexp_replace(F.col("Causality"), " \\(.*\\)", ""))))\
.withColumn("Adverse_Drug_Reaction_Term", 
            F.initcap((F.regexp_extract(F.col("Adverse_Drug_Reaction_Term"), "^([^/]+)", 1))))

# Cleaning Text: 
text_columns = ["Active_Substance", "SOC_If_No_Matching_PT", "LLT_If_No_Matching_PT", "MedDRA_PT_Text", "Adverse_Drug_Reaction_Term"]
for col_name in text_columns: 
    adr_df = adr_df.withColumn(col_name, F.initcap(F.trim(F.col(col_name))))

# save it as a new table
adr_df.write.mode("overwrite").saveAsTable("adr_data.adverse_drug_and_product_reaction.adr_table")
```
