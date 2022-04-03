# Airflow-in-Python
**In this repo, I'm practicing on Airflow with python3**

____________________________

> define the default arguments and create a DAG object for your workflow.
```
# Import the DAG object
from airflow.models import DAG
from datetime import datetime
# Define the default_args dictionary
default_args = {
  'owner': 'dsmith',
  'start_date': datetime(2020,1,14),
  'retries':2
}
# Instantiate the DAG object
etl_dag = DAG('example_etl', default_args=default_args)
```
____________________________

> You'd like to implement a simple scripts as an Airflow operators; You now want to add more components to the workflow. In addition to the cleanup.sh you have two more scripts, consolidate_data.sh and push_data.sh. These further process your data and copy to its final location.
```
# Import the DAG object
# Import the BashOperator
from airflow.models import DAG
from airflow.operators.bash_operator import BashOperator
from datetime import datetime

# Define the default_args dictionary
default_args = {
  'owner': 'dsmith',
  'start_date': datetime(2020,1,14),
  'retries':2
}
# Instantiate the DAG object
analytics_dag = DAG('example_etl', default_args=default_args)

# Define the BashOperator 
cleanup = BashOperator(
    task_id='cleanup_task',
    # Define the bash_command
    bash_command='cleanup.sh',
    # Add the task to the dag
    dag=analytics_dag
)

# Define a second operator to run the `consolidate_data.sh` script
consolidate = BashOperator(
    task_id='consolidate_task',
    bash_command='consolidate_data.sh',
    dag=analytics_dag)

# Define a final operator to execute the `push_data.sh` script
push_data = BashOperator(
    task_id='pushdata_task',
    bash_command='push_data.sh',
    dag=analytics_dag)

```
____________________________
> Task Instructions: 

  - DAG arguments are like settings for the DAG.
  - The below settings mention
  - the owner name: **Your Name**
  - when this DAG should run from: days_age(0) means today,
  - the email address where the alerts are sent to: **ramesh@somemail.com**
  - whether alert must be sent on failure,
  - whether alert must be sent on retry,
  - the number of retries in case of failure, and
  - the time delay between retries.
  - sample-etl-dag is the ID of the DAG. This is what you see on the web console.
  - We are passing the dictionary default_args, in which all the defaults are defined.
  - description helps us in understanding what this DAG does.
  - schedule_interval tells us how frequently this DAG runs. In this case every day. (days=1).
  - Fially: Define Tasks to write extract, transform and load
```
# import the libraries

from datetime import timedelta
# The DAG object; we'll need this to instantiate a DAG
from airflow import DAG
# Operators; we need this to write tasks!
from airflow.operators.bash_operator import BashOperator
# This makes scheduling easy
from airflow.utils.dates import days_ago

#defining DAG arguments

# You can override them on a per-task basis during operator initialization
default_args = {
    'owner': 'Muhammad Farouk',
    'start_date': days_ago(0),
    'email': ['ramesh@somemail.com'],
    'email_on_failure': True,
    'email_on_retry': True,
    'retries': 1,
    'retry_delay': timedelta(minutes=5),
}

# define the DAG
dag = DAG(
    dag_id='sample-etl-dag',
    default_args=default_args,
    description='Sample ETL DAG using Bash',
    schedule_interval=timedelta(days=1),
)


# define the tasks

# define the first task named extract
extract = BashOperator(
    task_id='extract',
    bash_command='echo "extract"',
    dag=dag,
)


# define the second task named transform
transform = BashOperator(
    task_id='transform',
    bash_command='echo "transform"',
    dag=dag,
)

# define the third task named load

load = BashOperator(
    task_id='load',
    bash_command='echo "load"',
    dag=dag,
)

# task pipeline
extract >> transform >> load

```
