# Weather Data Lakehouse
google colab notebook link: https://colab.research.google.com/drive/1QsZu394GuN9aBa15yBSzZVDPW3_IcZ_M?usp=sharing
## Setup Instruction
pip install delta-spark==2.1.0

This is a project that requires me to create a data lakehouse that can be used used as single source of truth for data users
that are interested at looking into device info and recorded data.
## Specific Information
[pipeline1](https://github.com/chiakangZacHung/Weather-Data-LakeHouse/blob/main/pipeline1.py) takes the latest device_information file from a user defined input directory and stores the file using timestamp in the specified output directory.
`python pipeline1.py [inputDirectory] [outputDirectory]`
[pipeline1_cleansed]([https://github.com/chiakangZacHung/Weather-Data-LakeHouse/blob/main/pipeline1.py](https://github.com/chiakangZacHung/Weather-Data-LakeHouse/blob/main/pipeline1_cleansed.py)) takes the device information from from a user defined input directory, which should be the output directory of pipeline1. The pipeline performs cleaning and store the file using timestamp in the specified output directory.
`python pipeline1_cleansed.py [inputDirectory] [outputDirectory]`
[pipeline2](https://github.com/chiakangZacHung/Weather-Data-LakeHouse/blob/main/pipeline2.py) takes the device data from from a user defined input directory. The pipeline store the file using timestamp in the specified output directory.
`python pipeline2.py [inputDirectory] [outputDirectory]`
[pipeline3](https://github.com/chiakangZacHung/Weather-Data-LakeHouse/blob/main/pipeline3.py) takes the cleansed device information from from a user defined input directory, and the device data from input directory 2. The pipeline aggregates two tables, store the file using timestamp in the directory called mergedData. The pipeline also aggregates the data per month and area and store them in directory "month" and "area" respectively.
`python pipeline3.py [inputDirectory1] [inputDirectory2]`
## General Information
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
