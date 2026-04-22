```Python
import pyspark.sql.functions as F

#Loading Raw Data: 
adr_df = spark.table("john_snow_labs_adverse_drug_and_product_reaction.drug_and_product_reaction.adverse_reaction_with_preferred_term_and_system_organ_class_codes")

# Cleaning Frequency, Gender, Age_Group, Causality and Adverse_Drug_Reaction_Term columns:
adr_df = adr_df\
.withColumn("Frequency", 
            F.initcap(F.when(F.col("Frequency").contains("Unknown"), "Not Specified")
                      .otherwise(F.col("Frequency"))))\
.withColumn("Gender", 
            F.initcap(F.when(F.col("Gender").contains("Not specified"), "Not Specified")
                      .otherwise(F.regexp_replace(F.col("Gender"), "^ADR occurs in | only$", ""))))\
.withColumn("Age_Group", 
            F.initcap(F.when(F.col("Age_Group").contains("Age category not"), "Not Specified")
                      .otherwise(F.regexp_replace(F.regexp_replace(F.col("Age_Group"), "^ADR occurs in |^ADR occurring in ", ""), " only$", ""))))\
.withColumn("Causality", 
            F.initcap(F.when(F.col("Causality").contains("Unassessable"), "Not Specified")
                      .otherwise(F.regexp_replace(F.col("Causality"), " \\(.*\\)", ""))))\
.withColumn("Adverse_Drug_Reaction_Term", 
            F.initcap((F.regexp_extract(F.col("Adverse_Drug_Reaction_Term"), "^([^/]+)", 1))))

# Cleaning Text: 
text_columns = ["Active_Substance", "SOC_If_No_Matching_PT", "LLT_If_No_Matching_PT", "MedDRA_PT_Text", "Adverse_Drug_Reaction_Term"]
for col_name in text_columns: 
    adr_df = adr_df.withColumn(col_name, F.initcap(F.trim(F.col(col_name))))

#Standardising Null Values:
null_replacements = {
    "Remarks_On_Date_Of_Most_Recent_SPC" : "Not Specified",
    "SOC_If_No_Matching_PT" : "Not Specified",
    "HLGT_If_No_Matching_PT" : "Not Specified",
    "HLT_If_No_Matching_PT" : "Not Specified", 
    "LLT_If_No_Matching_PT" : "Not Specified",
    "MedDRA_PT_Text" : "Not Specified", 
    "MedDRA_PT_Code" : "0", 
    "MedDRA_SOC_Code" : "0", 
    "Gender" : "Not Specified", 
    "Causality" : "Not Specified",
    "Is_Class_Warning" : "False",
    "Comment" : "Not Specified" }

adr_df = adr_df.fillna(null_replacements)

# save it as a new table
adr_df.write.mode("overwrite").saveAsTable("adr_data.adverse_drug_and_product_reaction.adr_table")
```
