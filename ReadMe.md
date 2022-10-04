# Weather Data Lakehouse
google colab notebook link: https://colab.research.google.com/drive/1QsZu394GuN9aBa15yBSzZVDPW3_IcZ_M?usp=sharing

This is a project that requires me to create a data lakehouse that can be used used as single source of truth for data users
that are interested at looking into device info and recorded data.

I wrote two [Spark](https://spark.apache.org/) pipelines that ingest and cleanse the data:
* one pipeline to process *device information* (the `csv` file)
* the other to process *device data* (the actual data points recorded by devices).

Pipelines can be composed of multiple steps and have to create the datasets needed to match the
requirements that are given below.

Additionally, as a basic example of report, create a third pipeline that:

* takes as input the cleansed data,
* joins the *device data* with the *device info*,
* aggregates the data per month and area,
* and computes average values.

### Data Users

The data created by the pipelines will serve three main users:

* **Data Scientists**: They run transformations on the cleansed data at scale to aggregate it,
  compute features and train models;
* **Data Analysts**: They run transformations on the cleansed data both at scale and on a periodic
  schedule to build aggregated tables for reporting purposes;
* **Device Developers**: They look into the ingested data to analyse the issues and monitor how
  these issues evolve over time.

### Requirements


* each dataset that is made available to the users must be stored using a [delta
  table](https://delta.io/);
* the project must provide configuration values that allow the user to execute it in multiple
  environments (i.e. dev, staging, prod). An example of configuration value could be the path of an
  input (or output) file.

#### Device information

The device information must be refreshed **every
week**, when a new version of the data is available. There is no specific transformation/cleansing
required.

#### Device data

The data must be ingested from the landing zone into the data lakehouse, cleansed to fix known
issues, and made available for analysis to data analysts and data scientists. It must be updated
**daily**.

The ingested data must be available to the developers of the data acquisition system as well, so
that they can query the data and analyse possible issues.

Moreover, it must be possible to reprocess past days if needed, both for reproducibility and in case
data scientists find out that a data cleansing step has to be added or modified.
