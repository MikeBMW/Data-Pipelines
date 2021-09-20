# Data-Pipelines
Project: Data Pipelines with Airflow
A music streaming company, Sparkify, has decided that it is time to introduce more automation and monitoring to their data warehouse ETL pipelines and come to the conclusion that the best tool to achieve this is Apache Airflow.

They have decided to bring you into the project and expect you to create high grade data pipelines that are dynamic and built from reusable tasks, can be monitored, and allow easy backfills. They have also noted that the data quality plays a big part when analyses are executed on top the data warehouse and want to run tests against their datasets after the ETL steps have been executed to catch any discrepancies in the datasets.

The source data resides in S3 and needs to be processed in Sparkify's data warehouse in Amazon Redshift. The source datasets consist of JSON logs that tell about user activity in the application and JSON metadata about the songs the users listen to.

Project Overview
This project will introduce you to the core concepts of Apache Airflow. To complete the project, you will need to create your own custom operators to perform tasks such as staging the data, filling the data warehouse, and running checks on the data as the final step.

We have provided you with a project template that takes care of all the imports and provides four empty operators that need to be implemented into functional pieces of a data pipeline. The template also contains a set of tasks that need to be linked to achieve a coherent and sensible data flow within the pipeline.

You'll be provided with a helpers class that contains all the SQL transformations. Thus, you won't need to write the ETL yourselves, but you'll need to execute it with your custom operators.

Example DAG
Example DAG


Good effort with the first submission. Nice job implementing all the required operators. There's an error with the table creation. Please complete the following required changes for the next submission.

[Required Changes]

Please fix the error in table creation or move the table creation task out of the main DAG
Remove the hard-coded tests from Data Quality operator.
[Note]

Please make sure the project runs to completion and loads the final tables without any errors.
To speed up, use a small subset of data while working on the pipe line:
For example:
Use "song_data/A/A/A" instead of full song data, "song_data"
Once you know everything works as expected you can switch to the full data set.
If you have any questions or need some help in fixing errors, please do ask in Knowledge Forum, https://knowledge.udacity.com/. There are mentors to answer your questions.
General
DAG can be browsed without issues in the Airflow UI

At Airflow home DAG listed without any errors. Able to browse the DAG contents.
The dag follows the data flow provided in the instructions, all the tasks have a dependency and DAG begins with a start_execution task and ends with a end_execution task.

DAG is constructed as per specification, with correct dependencies.
You can move the table creation task out of the main DAG into a separate DAG or manually create the tables.
Here's an easy to understand guide on task dependencies: https://www.astronomer.io/guides/managing-dependencies/

Dag configuration
DAG contains default_args dict, with the following keys:

Owner
Depends_on_past
Start_date
Retries
Retry_delay
Catchup
The default arguments are correctly defined.
The DAG object has default args set

The arguments are set to DAG object.
The DAG should be scheduled to run once an hour

Scheduled to run hourly.
The varied options of scheduling can be found here in this documentation, https://airflow.apache.org/docs/1.10.1/scheduler.html

Staging the data
There is a task that to stages data from S3 to Redshift. (Runs a Redshift copy statement)

The DAG results in the below error:

[2021-08-06 06:54:28,078] {create_table.py:19} INFO - Executing creating tables in Redshift.
[2021-08-06 06:54:28,078] {models.py:1788} ERROR - [Errno 2] No such file or directory: '/home/workspace/airflow/create_tables.sql'
Traceback (most recent call last):
  File "/opt/conda/lib/python3.6/site-packages/airflow/models.py", line 1657, in _run_raw_task
    result = task_copy.execute(context=context)
  File "/home/workspace/airflow/plugins/operators/create_table.py", line 20, in execute
    queries =  open('/home/workspace/airflow/create_tables.sql', 'r').read()
FileNotFoundError: [Errno 2] No such file or directory: '/home/workspace/airflow/create_tables.sql'
[2021-08-06 06:54:28,084] {models.py:1819} INFO - Marking task as FAILED.
Please remove the table creation task or keep the create_tables.sql at its location.
Instead of running a static SQL statement to stage the data, the task uses params to generate the copy statement dynamically

The code for this part looks ok. We will evaluate after fixing the error.
The operator contains logging in different steps of the execution

Progress and informational messages are logged through log.info.
Good information about logging in Airflow: https://www.astronomer.io/guides/logging/

The SQL statements are executed by using a Airflow hook

The 'redshift' connection defined through Airflows connection list used.
Loading dimensions and facts
Dimensions are loaded with on the LoadDimension operator

The code for this part looks ok. We will evaluate after fixing the error.
Facts are loaded with on the LoadFact operator

The code for this part looks ok. We will evaluate after fixing the error.
Instead of running a static SQL statement to stage the data, the task uses params to generate the copy statement dynamically

The code for this part looks ok. We will evaluate after fixing the error.
The DAG allows to switch between append-only and delete-load functionality

The code for this part looks ok. We will evaluate after fixing the error.
Data Quality Checks
Data quality check is done with correct operator

The Data quality operator implemented.
The DAG either fails or retries n times

The code for this part looks ok. We will evaluate after fixing the error.
Operator uses params to get the tests and the results, tests are not hard coded to the operator

The test query, SELECT COUNT(*) FROM {table} is hardcoded inside the operator. Only the table is configurable here. Make Data Quality operator more generalizable by moving the hardcoded tests outside the operator.

Please pass a list of tests and expected results from the DAG to the operator as a parameter. Inside the operator loop through this list and run the tests on each.
Example code:

        for check in self.dq_checks:
            sql = check.get('check_sql')
            exp_result = check.get('expected_result')

            records = redshift.get_records(sql)[0]
            # compare with the expected results
            ...
In dag, the tests can be specified as below:

    dq_checks=[
        {'check_sql': "SELECT COUNT(*) FROM users WHERE userid is null", 'expected_result': 0},
        {'check_sql': <Could be another test>, 'expected_result': 0}
    ]
