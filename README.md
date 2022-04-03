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
