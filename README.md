# Apache Airflow

[Apache Airflow](https://airflow.apache.org/docs/apache-airflow/stable/) (or simply Airflow) is a platform to programmatically author, schedule, and monitor workflows.

When workflows are defined as code, they become more maintainable, versionable, testable, and collaborative.

Use Airflow to author workflows as directed acyclic graphs (DAGs) of tasks. The Airflow scheduler executes your tasks on an array of workers while following the specified dependencies. Rich command line utilities make performing complex surgeries on DAGs a snap. The rich user interface makes it easy to visualize pipelines running in production, monitor progress, and troubleshoot issues when needed.


## Quick Start

- Pull Docker image from docker registry.

```bash
docker pull ghcr.io/technobureau/airflow:latest
```

- Initialize the airflow database

```bash
cd /opt/airflow && mkdir -p ./dags ./logs ./plugins
echo -e "AIRFLOW_UID=$(id -u)\nAIRFLOW_GID=0" > .env
docker-compose up airflow-init
```
- Start airflow docker. Please make sure docker-compose.yaml copied to /opt/airflow on the server before start all services of airflow.

```bash
cd /opt/airflow/
docker-compose up
```

## Project Focus

Airflow works best with workflows that are mostly static and slowly changing. When the DAG structure is similar from one run to the next, it clarifies the unit of work and continuity. Other similar projects include [Luigi](https://github.com/spotify/luigi), [Oozie](https://oozie.apache.org/) and [Azkaban](https://azkaban.github.io/).

Airflow is commonly used to process data, but has the opinion that tasks should ideally be idempotent (i.e., results of the task will be the same, and will not create duplicated data in a destination system), and should not pass large quantities of data from one task to the next (though tasks can pass metadata using Airflow's [Xcom feature](https://airflow.apache.org/docs/apache-airflow/stable/concepts.html#xcoms)). For high-volume, data-intensive tasks, a best practice is to delegate to external services specializing in that type of work.

Airflow is not a streaming solution, but it is often used to process real-time data, pulling data off streams in batches.

## Requirements

Apache Airflow is tested with:

|                      | Main version (dev)        | Stable version (2.1.4)   |
| -------------------- | ------------------------- | ------------------------ |
| Python               | 3.6, 3.7, 3.8, 3.9        | 3.6, 3.7, 3.8, 3.9       |
| Kubernetes           | 1.18, 1.19, 1.20          | 1.18, 1.19, 1.20         |
| PostgreSQL           | 9.6, 10, 11, 12, 13       | 9.6, 10, 11, 12, 13      |
| MySQL                | 5.7, 8                    | 5.7, 8                   |
| SQLite               | 3.15.0+                   | 3.15.0+                  |
| MSSQL(Experimental)  | 2017, 2019                |                          |

**Note**: MySQL 5.x versions are unable to or have limitations with
running multiple schedulers -- please see the [Scheduler docs](https://airflow.apache.org/docs/apache-airflow/stable/scheduler.html).
MariaDB is not tested/recommended.

**Note**: SQLite is used in Airflow tests. Do not use it in production. We recommend
using the latest stable version of SQLite for local development.



## Installing from PyPI

We publish Apache Airflow as `apache-airflow` package in PyPI. Installing it however might be sometimes tricky
because Airflow is a bit of both a library and application. Libraries usually keep their dependencies open, and
applications usually pin them, but we should do neither and both simultaneously. We decided to keep
our dependencies as open as possible (in `setup.py`) so users can install different versions of libraries
if needed. This means that `pip install apache-airflow` will not work from time to time or will
produce unusable Airflow installation.

To have repeatable installation, however, we keep a set of "known-to-be-working" constraint
files in the orphan `constraints-main` and `constraints-2-0` branches. We keep those "known-to-be-working"
constraints files separately per major/minor Python version.
You can use them as constraint files when installing Airflow from PyPI. Note that you have to specify
correct Airflow tag/version/branch and Python versions in the URL.


1. Installing just Airflow:

> Note: Only `pip` installation is currently officially supported.

While it is possible to install Airflow with tools like [Poetry](https://python-poetry.org) or
[pip-tools](https://pypi.org/project/pip-tools), they do not share the same workflow as
`pip` - especially when it comes to constraint vs. requirements management.
Installing via `Poetry` or `pip-tools` is not currently supported.

If you wish to install Airflow using those tools, you should use the constraint files and convert
them to the appropriate format and workflow that your tool requires.


```bash
pip install 'apache-airflow==2.1.4' \
 --constraint "https://raw.githubusercontent.com/apache/airflow/constraints-2.1.4/constraints-3.7.txt"
```

2. Installing with extras (i.e., postgres, google)

```bash
pip install 'apache-airflow[postgres,google]==2.1.4' \
 --constraint "https://raw.githubusercontent.com/apache/airflow/constraints-2.1.4/constraints-3.7.txt"
```

For information on installing provider packages, check
[providers](http://airflow.apache.org/docs/apache-airflow-providers/index.html).



## User Interface

- **DAGs**: Overview of all DAGs in your environment.

  ![DAGs](https://raw.githubusercontent.com/apache/airflow/main/docs/apache-airflow/img/dags.png)

- **Tree**: Tree representation of a DAG that spans across time.

  ![Tree](https://raw.githubusercontent.com/apache/airflow/main/docs/apache-airflow/img/tree.png)

- **Graph**: Visualization of a DAG's dependencies and their current status for a specific run.

  ![Graph](https://raw.githubusercontent.com/apache/airflow/main/docs/apache-airflow/img/graph.png)

- **Task Duration**: Total time spent on different tasks over time.

  ![Task Duration](https://raw.githubusercontent.com/apache/airflow/main/docs/apache-airflow/img/duration.png)

- **Gantt**: Duration and overlap of a DAG.

  ![Gantt](https://raw.githubusercontent.com/apache/airflow/main/docs/apache-airflow/img/gantt.png)

- **Code**: Quick way to view source code of a DAG.

  ![Code](https://raw.githubusercontent.com/apache/airflow/main/docs/apache-airflow/img/code.png)
