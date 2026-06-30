
from pyspark.sql.types import (
    StructType, StructField,
    IntegerType, StringType, DateType
)

from pyspark.sql.functions import col,when



x=spark.table("people_delta").select(
    "firstName",
    "salary",
    "lastName",
    when(col("salary").isNull(), "salary_missing")
    .when(col("salary") <= 50000, "low_salary")
    .when ((col("salary")>50000) & (col("salary") < 60000), "medium_salary")
    .when(col("salary") >= 60000, "high_salary")
    .otherwise("unknown")
    .alias("salary_category")                         
).orderBy(col("salary").desc())



x.write.format("delta")\
    .mode("overwrite")\
    .option("overwriteSchema","true")\
    .saveAsTable("people_salary")


spark.table("people_salary").show()
