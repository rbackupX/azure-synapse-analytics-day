# Exercise 1 - Explore the Data Lake with Azure Synapse serverless SQL pool and Azure Synapse Spark

In this exercise, you will explore data using the engine of your choice (SQL or Spark).

Understanding data through data exploration is one of the core challenges faced today by data engineers and data scientists. Depending on the data's underlying structure and the specific requirements of the exploration process, different data processing engines will offer varying degrees of performance, complexity, and flexibility.

In Azure Synapse Analytics, you can use either the SQL Serverless engine, the big-data Spark engine, or both.

The tasks you will perform in this exercise are:

- Explore the Data Lake with SQL On-demand and Spark
  - Task 1 - Explore the Data Lake with Azure Synapse serverless SQL pool 
  - Task 2 - Explore the Data Lake with Azure Synapse Spark

## Task 1 - Explore the Data Lake with Azure Synapse serverless SQL pool 

In this task, you will browse your data lake using serverless SQL pool.

1. In a Microsoft Edge web browser, navigate to the Azure portal (`https://portal.azure.com`) and login with your credentials. Then select **Resource groups**.

   ![Open Azure resource group](./media/00-open-resource-groups.png "Azure resource groups")

2. Select the **Synapse Analytics** resource group.

   ![Open Synapse Analytics resource group](./media/00-open-synapse-resource-group.png "Resources list")

3. Select **SQLPool01** and **resume** it before starting the exercise.

   ![SQLPool01 is highlighted.](media/select-sql-pool.png "SQLPool01")

   ![Resume sqlpool](./media/00-resume-sqlpool.png "Resume")

4. Return to the resource group, then select the **Synapse Analytics** workspace.

   ![Open Azure Synapse Analytics workspace](./media/00-open-workspace.png "Azure Synapse workspace")

5. On the Synapse workspace blade, open Synapse Analytics Studio by navigating to the **Workspace web URL** from the overview page.

   > You can also Open synapse studio by clicking on **Open** under **Getting started->Open synapse studio**

   ![The Launch Synapse Studio button is highlighted on the Synapse workspace toolbar.](media/ex01-open-synapse-studio.png "Launch Synapse Studio")

6. In Synapse Analytics Studio, navigate to the `Data` hub.

   ![Open Data hub in Synapse Analytics Studio](./media/data-hub.png)

7. Switch to the `Linked` tab **(1)**. Under `Azure Data Lake Storage Gen2` **(2)**, expand the primary data lake storage account, and then select the `wwi` file system **(3)**.

   ![The ADLS Gen2 storage account is selected.](media/storage-factsale-parquet.png "ADLS Gen2 storage account")

8. Inside the selected file system, double-click to navigate to `factsale-parquet` -> `2012` -> `Q1` -> `InvoiceDateKey=2012-01-01` **(4)**.

9. Once you are in `InvoiceDateKey=2012-01-01` right-click the Parquet file and select `New SQL script - Select TOP 100 rows`.

   > A script is automatically generated. Run this script to see how SQL on demand queries the file and returns the first 100 rows of that file with the header, allowing you to explore data in the file easily.

   ![Start new SQL script from data lake file](./media/ex01-sql-on-demand-01.png "Create a new SQL script")

10. Ensure the newly created script is connected to the `Built-in` pool representing serverless SQL pool and select `Run`. Data is loaded by the built-in SQL pool and processed as if it came from any regular relational database.

    ![Run SQL script on data lake file](./media/ex01-sql-on-demand-02.png "Execute SQL script")

11. Let us change the initial script to load multiple Parquet files at once.

    - In line 2, replace `TOP 100 *` with `COUNT(*)`.
    - In line 5, replace the path to the individual file with

    ```python
    https://<yourdatalake storage account name>.dfs.core.windows.net/wwi/factsale-parquet/2012/Q1/*/*
    ```

    > Note: Replace '< yourdatalake storage account name >' with the **Storage Account Name** provided in the environment details section on the Lab Environment tab on the right.

12. Select `Run` to re-run the script. You should see a result of `2991716`, which is the number of records contained in all the Parquet files within the `factsale-parquet/2012/Q1` directory.

    ![Run SQL on-demand script loading multiple Parquet data lake files](./media/ex01-sql-on-demand-03.png)

13. In Azure Synapse Analytics Studio, navigate to the `Develop` hub.

    ![Develop hub.](media/develop-hub.png "Develop hub")

14. Select the `Exercise 1 - Read with SQL on-demand` **(1)** SQL script. Connect to **Built-in (2)**. Select **Run (3)** to execute the script.

    ![Run SQL on-demand script loading multiple CSV data lake files](./media/ex01-sql-on-demand-04.png)

    > This query demonstrates the same functionality, except this time, it loads CSV files instead of Parquet ones (notice the `factsale-csv` folder in the path). Parquet files are compressed and store data in columnar format for efficient querying, compared to CSV files that are raw representations of data but easily processed by many systems. You can often encounter many file types stored in a data lake and must know how to access and explore those files. For instance, when you access CSV files, you need to specify the format, field terminator, and other properties to let the query engine understand how to parse the data. In this case, we determine the value of `2` for FIRSTROW. This indicates that the first row of the file must be skipped because it contains the column header.
    >
    > Here, we use WITH to define the columns in the files. You must use WITH when using a bulk rowset (OPENROWSET) in the FROM clause. Also, defining the columns enables you to select and filter the values within.

## Task 2 - Explore the Data Lake with Azure Synapse Spark

1. Navigate to the `Data` hub, browse to the data lake storage account folder `wwi/factsale-parquet/2012/Q1/InvoiceDateKey=2012-01-01`, then right-click the Parquet file and select `New notebook->Load Data frame`

   ![Start new Spark notebook from data lake file](./media/ex01-spark-notebook-001.png "Create a new Spark notebook")

2. This will generate a notebook with PySpark code to load the data in a dataframe and display 10 rows with the header.

   ![New Spark notebook from data lake file](./media/ex01-spark-notebook-002.png "Review the notebook")

3. Attach the notebook to a Spark pool **(1)**.

   ![Run Spark notebook on data lake file](./media/ex01-attachsparkpool01.png "Attach notebook to Spark pool")

4. Select **Run all** on the notebook toolbar **(2)** to execute the notebook.

   > **Note**: The first time you run a notebook in a Spark pool, Synapse creates a new session. This can take approximately 3 minutes.
   
    ![Waiting for the Spark pool to start.](./media/ex01-attachsparkpool01waiting.png "Waiting for the Spark pool to start.")

5. As you can see, the output of the dataframe is displayed with 10 rows. To  display 100 rows with the header, replace the last line of code with the following:

   ```python
   display(df.limit(100))
   ```

6. Re-run the notebook again to see the result.

   ![Improve dataset formatting in Spark notebook](./media/ex01-spark-notebookrun-04.png "Execute notebook")

7. Notice the included charting capabilities that enable visual exploration of your data. Switch to **Chart** view **(1)**. Select **View Options (2)** and change the **Key** to `CustomerKey` **(3)** and **Values** to `CityKey` **(4)**. Finally, select **Apply** to apply changes **(5)**.

    ![View charts on data in Spark notebook](./media/ex01-spark-notebook-05.png "Review charted data")

8. Hover over the area just below the cell in the notebook, then select **{} Add code** to add a new cell.

   ![The add code button is highlighted.](media/add-cell.png "Add code")

9. Paste the following into the cell, and **replace** `YOUR_DATALAKE_NAME` **(1)** with the name of your **Storage Account Name** provided in the environment details section on the Lab Environment tab on the right. You can also copy it from the first cell of the notebook above.

   ```python
   data_path = spark.read.load(
      'abfss://wwi@YOUR_DATALAKE_NAME.dfs.core.windows.net/factsale-csv/2012/Q1/*/*',
      format='csv',
      sep="|",
      header=True)

   display(data_path.limit(100))
   ```

10. Select the **Run cell** button **(2)** to execute the new cell.

    ![The new cell is displayed, and the run cell button is highlighted.](media/notebook-new-csv-cell.png "New cell to explore CSV files")

    > This notebook demonstrates the same functionality, except this time, it loads CSV files instead of Parquet ones (notice the `factsale-csv` folder in the path).

11. **Important**: If you are continuing to Exercise 2 now, _leave this notebook open for the first task_ of the next exercise. This way, you can continue to use this notebook and the running Spark session, saving you time.
