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

# init a dvc projct
dvc init

# This will generate three internal files
git status

# expected output
On branch main
Your branch is ahead of 'origin/main' by 1 commit.
  (use "git push" to publish your local commits)

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   .dvc/.gitignore
        new file:   .dvc/config
        new file:   .dvcignore

# add the generated files to git
git add .dvc .dvcignore
 
git commit -m "init dvc project"

git push origin main
```

## Track a data

```shell
touch data/test.txt

dvc add data/test.txt

# untracked the data from git, and add it to .gitignore
git restore data/test.txt

# to remove it after commit
git rm -r --cached 'data/test.txt'

# commit the change
git commit -m "stop tracking data/test.txt"
```

It will not work, if the `test.txt` is already tracked by git. 

After the clean, re-run `dvc add data/test.txt`.

```shell
# you will find out a new file test.txt.dvc is generated
# below is the content of the file
outs:
- md5: d41d8cd98f00b204e9800998ecf8427e
  size: 0
  hash: md5
  path: test.txt

```

## Store the data in a remote store

To share data by using dvc, you need to add a remote data store(e.g. S3, NFS, SSH).

### Add a remote store

```shell
mkdir /tmp/dvcstore
dvc remote add -d local_remote /tmp/dvcstore

# expected output
Setting 'local_remote' as a default remote.

```

### Push data to remote store

After adding a remote store, we can upload data to remote store
```shell
dvc push

# expected output
Collecting                                                                                                                                                                                                                                                                                                                                                  |1.00 [00:00,  321entry/s]
Pushing
1 file pushed                                                  
```

### Download data from remote store

Once DVC-tracked data and models are stored remotely, they can be downloaded with `dvc pull` when needed 
(e.g. in other copies of this project). Usually, we run it after git pull or git clone.

```shell
dvc pull

# expected output
Collecting                                                                                                                                                                                                                                                                                                                                                  |1.00 [00:00,  226entry/s]
Fetching
Building workspace index                                                                                                                                                                                                                                                                                                                                    |2.00 [00:00,  887entry/s]
Comparing indexes                                                                                                                                                                                                                                                                                                                                          |3.00 [00:00, 1.88kentry/s]
Applying changes                                                                                                                                                                                                                                                                                                                                            |0.00 [00:00,     ?file/s]
Everything is up to date.
```

## Make some changes on the data

Add some text to the `test.txt`. Before it was empty.
```shell
#  after adding text, run dvc add again to track the latest version
dvc add data/test.txt

# run dvc push to upload the changes to the remote storage, followed by a git commit to track them:
dvc push

# track the new dvc metadata of test.txt via git
git add data/test.txt.dvc 
git commit -m "Dataset updates"
git push

```

## Go back to the older version of the data

```shell
# get the previous dvc metadata
git checkout HEAD~1 data/test.txt.dvc

# run dvc checkout to sync the data from the remote store with older version of the dvc metadata, so you get older version of data
dvc checkout
```