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
  'start_date': datetime(2022,3,14),
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
  'start_date': datetime(2022,3,14),
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
____________________________

```
> implement a task to download and save a file to the system within Airflow.
```
# Import the DAG object
# Import the PythonOperator
from airflow.models import DAG
from airflow.operators.python_operator import PythonOperator
import requests
from datetime import datetime

# Define the default_args dictionary
default_args = {
  'owner': 'dsmith',
  'start_date': datetime(2022,3,14),
  'retries':2
}

# Instantiate the DAG object
process_sales_dag = DAG('example_etl', default_args=default_args)
def pull_file(URL, savepath):
    r = requests.get(URL)
    with open(savepath, 'wb') as f:
        f.write(r.content)   
    # Use the print method for logging
    print(f"File pulled from {URL} and saved to {savepath}")

# Create the task
pull_file_task = PythonOperator(
    task_id='pull_file',
    # Add the callable
    python_callable=pull_file,
    # Define the arguments
    op_kwargs={'URL':'http://dataserver/sales.json', 'savepath':'latestsales.json'},
    dag=process_sales_dag
)
```
____________________________

> Task Instructions:   
  - DAG arguments are like settings for the DAG.
  - The below settings mention
  - the owner name: **Your Name**
  - Set the start date of the DAG to November 1, 2019
  - the email address where the alerts are sent to: **airflowresults@datacamp.com**
  - whether alert must be sent on failure.
  - whether alert must be sent on retry.
  - the number of retries in case of failure equal to 3
  - the time delay between retries is 20 minutes
  - Use the cron syntax to configure a schedule of every Wednesday at 12:30pm
```
# Import the DAG object
from airflow.models import DAG
from datetime import datetime

 # Update the scheduling arguments as defined
default_args = {
  'owner': 'Muhammad Farouk',
  'start_date': datetime(2019,11, 1),
  'email': ['airflowresults@datacamp.com'],
  'email_on_failure': True,
  'email_on_retry': True,
  'retries': 3,
  'retry_delay': timedelta(minutes=20)
}

dag = DAG('update_dataflows', default_args=default_args, schedule_interval='30 12 * * 3')
)
```
____________________________

> Task Instructions: 

  - DAG arguments are like settings for the DAG.
  - The below settings mention
  - the owner name: **Your Name**
  - when this DAG should run from: days_age(0) means today,
  - the email address where the alerts are sent to: **ramesh@somemail.com**
  - whether alert must be sent on failure.
  - whether alert must be sent on retry.
  - the number of retries in case of failure equal to 2
  - the time delay between retries is 5 minutes
  - sample-etl-dag is the ID of the DAG. This is what you see on the web console.
  - We are passing the dictionary default_args, in which all the defaults are defined.
  - description helps us in understanding what this DAG does.
  - schedule_interval tells us how frequently this DAG runs. In this case every day. (days=1).
  - Finally: Define Tasks to write extract, transform and load
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
____________________________
> Task Instructions: 

  - DAG arguments are like settings for the DAG.
  - The below settings mention
  - We are passing the dictionary default_args, in which all the defaults are defined.
  - description helps us in understanding what this DAG does.
  - schedule_interval tells us how frequently this DAG runs. In this case every day
  - Define and run the tasks
 
```

#import Loads
from airflow.models import DAG
from airflow.operators.bash_operator import BashOperator
from airflow.contrib.sensors.file_sensor import FileSensor
from datetime import datetime

# define the DAG
report_dag = DAG(
    dag_id = 'execute_report',
    schedule_interval = "0 0 * * *"
)

#define the FileSensor, mode is 'reschedule' means to give up the task when faile and try again later
precheck = FileSensor(
    task_id='check_for_datafile',
    filepath='salesdata_ready.csv',
    start_date=datetime(2022,3,15),
    mode='reschedule',
    dag=report_dag
)

#define the task generate_report_task
generate_report_task = BashOperator(
    task_id='generate_report',
    bash_command='generate_report.sh',
    start_date=datetime(2022,3,15),
    dag=report_dag
)

precheck >> generate_report_task
```
____________________________
