# Introduction DVC

**Data Version Control (DVC)** is a data versioning and reproducibility tool for machine learning and data science workflows. 

It provides `Git-like data versioning control` service for Large Files, machine learning models, and intermediate files without storing them in Git. 
It tracks file versions and their relationships to code, enabling full reproducibility.

It has the below main feature:

1. Storage Abstraction: DVC stores large files outside Git (e.g., in S3, GCS, Azure, local disk), but tracks them using small text .dvc files inside Git. 
                       This prevents Git from bloating while maintaining linkage between code and data.

2. Pipeline Management: DVC defines ML pipelines as a directed acyclic graph (DAG). Each stage (e.g., preprocess → train → evaluate) is tracked, ensuring reproducibility and enabling incremental builds.

3. Reproducibility: DVC associates code, data, parameters, and results in a single versioned snapshot. This guarantees that a past result can be regenerated precisely by checking out the Git repo and running dvc repro.

4. Remote Data Sync: Supports pushing and pulling data/models to/from shared remote storage. Teams can collaborate efficiently without sharing huge files via Git.

5. Checksum-based Tracking: DVC uses checksums (MD5/SHA) to detect changes and avoid redundant computation or duplication.

For more details, you can visit their official [web site](https://dvc.org/doc).

## Install DVC

```shell
# create a python virtual env

# install dvc via pip
pip install dvc
```

## Init a dvc project

```shell
# I have a git project called dvc_example
cd dvc_example


```