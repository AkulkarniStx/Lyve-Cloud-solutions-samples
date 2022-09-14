
# Tar File Index and Extract Tool

             _____                      _____                _
            |_   _|  __ _  _ __        |_   _|  ___    ___  | |
              | |   / _` || '__| _____   | |   / _ \  / _ \ | |
              | |  | (_| || |   |_____|  | |  | (_) || (_) || |
              |_|   \__,_||_|            |_|   \___/  \___/ |_|
              

## Introduction
This script will perform 2 functions.
1. Index a give tar file or tar files in a directory
2. Extract only those files inside a tar given the names of the files inside the tar and the tar file name from an
S3 bucket.

The idea is to minimize the number of uploads to an s3 bucket by taring a lot of small files into a big tar.
Then upload the tar to a bucket. In order to only pull out small chunk of the files in a big tar, the index is used to
find the location of each file within the tar. The start and end location of the files is used along with get byterange
operation on an S3 bucket to achieve this. 

## Requirements
Ability to run Python 3.8 and above

## Known Limitations 
This is a prrof of concept for Tar file Indexing capabilities and Partial Tar File retrieval capabilities. 
Additional features or customemization is left up to the reader.


## Running Steps
**Step 1:** Get your Lyve Cloud bucket credentials.   
Here's what you'll need:
* Access Key
* Secret key
* Endpoint URL

**Step 2:** 
Config - S3 connection config details are read from conn.conf in config directory
which is at the same level as tar_tool.py

1. Please replace the placeholders in conn.conf with valid Access and Secret
key with access to the desired bucket.
2. The tar file needs to be uploaded to the desired bucket after indexing along
with its index file.
3. To test run this use the sample tar files. Create index, upload tar file along
with index file to bucket, lastly extract a file or two from the tar.

## Usage
The usage of the tool is as follows:

usage: tar_tool.py [-h] [--tarfile TARFILE] [--path PATH] [--extract EXTRACT]
                   [--outputpath OUTPUTPATH] [--bucketname BUCKETNAME]
                   [--configfile CONFIGFILE]

optional arguments:
  -h, --help            show this help message and exit
  --tarfile TARFILE     Name of the tarfile that needs to be indexed
  --path PATH           Path to a directory containing tarfiles, all *.tar
                        files in dir will be indexed
  --extract EXTRACT     Comma seperated list of the tarfile and files to be
                        extracted from that tarfile
  --outputpath OUTPUTPATH
                        Path to a directory where extracted files will be kept
  --bucketname BUCKETNAME
                        Name of the S3 bucket to get data from
  --configfile CONFIGFILE
                        Path to config File

## Results 
Example of Index a tarfile
python3 C:\Users\akulkarni\tools\index-tar\tar_tool.py
--tarfile C:\Users\akulkarni\tools\index-tar\tfile.tar

Sample Output
---------------------------------------
Running Tar Tool with following options:
   tarfile: C:\Users\akulkarni\tools\index-tar\tfile.tar
   path: None
   extract: None
   outputpath: C:\Users\akulkarni\tools
   bucketname: tar-exp0
   configfile: C:\Users\akulkarni\tools\index-tar\config\conn.conf
Starting Indexing for C:\Users\akulkarni\tools\index-tar\tfile.tar

Done indexing tar C:\Users\akulkarni\tools\index-tar\tfile.tar,
Index file: C:\Users\akulkarni\tools\index-tar\tfile.index


Example of Extract some files from tarfile
python3 C:\Users\akulkarni\tools\index-tar\tar_tool.py
--extract C:\Users\akulkarni\tools\index-tar\tfile.tar,one.txt,four.txt
--outputpath C:\Users\akulkarni\tools\index-tar\output
--bucket tar-exp0
Note: The tool creates a directory with the name of tarfile under given output path, and extracts file in it.

Sample Output
---------------------------------------
Running Tar Tool with following options:
   tarfile: None
   path: None
   extract: C:\Users\akulkarni\tools\index-tar\tfile.tar,one.txt,four.txt
   outputpath: C:\Users\akulkarni\tools\index-tar\output
   bucketname: tar-exp0
   configfile: C:\Users\akulkarni\tools\index-tar\config\conn.conf
Collecting Index file tfile.index
Done writing the Index file tfile.index
Extracting files to C:\Users\akulkarni\tools\index-tar\output\tfile
Collecting one.txt from Byte Range: bytes=62976-63017
Done writing the file one.txt
Collecting four.txt from Byte Range: bytes=512-62363
Done writing the file four.txt

Done extracting ['one.txt', 'four.txt'] to C:\Users\akulkarni\tools\index-tar\output\tfile

## Tested by
* August 22, 2021: Bari Arviv (bari.arviv@seagate.com) on Ubuntu 20.4
* month day, year: full_name (email) on your_environment

## **Note:** All files should be uploaded (all the files you used for the integration solution) under one folder, including the README.md file. 
### Project Structure

This section will describe the representation of each of the folders or files in the structure.
```
.
├── README.md
├── config
│   └── conn.conf
├── output
│   └── placeholder for output
├── __init__.py
├── tar_tool.py
├── tfile.index
├── tfile.tar
├── tfile1.index
├── tfile1.tar

```