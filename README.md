The AGC meeting confirmed that the team can extend their existing CPAD environment (EAI: 12087) for Communications Surveillance rather than creating a separate environment, using the same resource group with internal segregation through Databricks ACLs, new SQL databases, and storage containers to separate market surveillance and communications data access. However, they discovered potential compliance issues - the current environment may not be in the proper landing zone and the previously approved Azure Speech Services component is now broken due to Microsoft's AI rebranding. Next steps include Stephen creating an updated architecture diagram with Surveil-X data flow, checking GRC compliance status for potential landing zone migration, decommissioning the old Speech Services component, and proceeding with technical implementation using Databricks' granular permission controls to achieve the required data segregation between teams without needing a new AGC submission.

**Can you or any person who can brainstorm which pipelines have dependencies and which are not? We can create groups for the pipelines which are not dependent, so that they can run in parallel. Once the independent patterns acquire the database lock, the other jobs or tasks will wait. If we are writing a single row in the alert database, then using stored procedures can achieve parallelism. This is my understanding - kindly correct me if I am missing or have overlooked anything. Please review this.**
I’m currently working on the proxy pipeline where I’ve added components such as an Until activity. The idea is that whenever any of the pattern pipelines (e.g., pattern A, pattern B, etc.) are triggered, they should run in parallel. For example, if we have eight pipelines, all eight should execute simultaneously and eventually converge at the proxy pipeline I’ve built.

Within the proxy pipeline, the Until activity is designed to process each pattern sequentially—row by row—while writing to the alert database. During this process, if one pipeline is writing to the database and another tries to write at the same time, it should wait until the current write is complete. This logic is what I’m trying to implement.

I’ve already started building the structure: the proxy pipeline has been created, a test notebook is in place, and it's integrated with the necessary ADF objects. The next steps involve testing the connections and validating the logic with sample data.

I’ll continue to keep you updated on the progress.
try:
    json_data = json.loads(input_dataframe_json_str)
    alerts_df_spark = spark.createDataFrame(json_data, schema=alert_schema)
except json.JSONDecodeError:
    alerts_df_spark = spark.createDataFrame([], schema=alert_schema)
    print("Received invalid JSON for writing. No alerts to process.")
