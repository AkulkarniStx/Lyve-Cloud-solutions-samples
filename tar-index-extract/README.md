
# Using Tar File Index to extract data efficiently

             _____                      _____                _
            |_   _|  __ _  _ __        |_   _|  ___    ___  | |
              | |   / _` || '__| _____   | |   / _ \  / _ \ | |
              | |  | (_| || |   |_____|  | |  | (_) || (_) || |
              |_|   \__,_||_|            |_|   \___/  \___/ |_|
              

## Introduction
This script can perform two functions, index a tar file and partial extract from the tar file using extract function.

1. First create index for a given tar file or tar files in a directory. Once a tar is indexed, the tar file and the corresponding index file are uploaded to the bucket. This is a prerequisite to the extract step.
2. When there is a need to extract some data sitting in an S3 bucket, the extract function can be used. It will extract only the given filename(s) in the given tar name(s) from the S3 bucket. 

The idea is to minimize the number of uploads to an s3 bucket by tarring a lot of small files into a big tar.
This helps with a better throughput during upload process. 

When a few of the files are needed some at a later time, the whole tar is not needed to be downloaded. This saves time and network costs during download.
To only get a small chunk of the files that are part of a big tar, the index is used to find the location of each file within the tar. The start and end location of the file is used along with *get byterange* operation on an S3 bucket to achieve this. 

## Requirements
* Ability to run Python 3.9 and above
* Following Python modules/libraries
  - ArgumentParser from argparse
  - boto3
  - csv
  - datetime
  - tarfile
  - tprint from art (for the banner only)

## Known Limitations 
This is a proof of concept for Tar file Indexing capabilities and Partial Tar File retrieval capabilities. 
Additional features or customization is left up to the reader.

## Running Steps
**Step 1:** Get your Lyve Cloud bucket credentials.   
Here's what you'll need:
* Access Key
* Secret key
* Endpoint URL

**Step 2:** 
Config - S3 connection config details are read from conn.conf in config directory
which is at the same level as tar_tool.py

1. Replace the placeholders in conn.conf with valid Access and Secret
key with access to the desired bucket.
2. The tar file needs to be uploaded to the desired bucket after indexing along
with its index file. Note: this step is a prerequisite to using Extract capability.
3. To test this tool out 
    -   First Create index for a tar file.
    -   Next upload the tar file along with index file to the bucket.
    -   Lastly extract a file or two from the tar.
    -   You can use the sample tar files included here.

## Usage
The usage of the tool is as follows:
```
usage: tar_tool.py [-h] [--tarfile TARFILE] [--path PATH] [--extract EXTRACT]
                   [--outputpath OUTPUTPATH] [--bucketname BUCKETNAME]
                   [--configfile CONFIGFILE]

optional arguments:
  -h, --help            show this help message and exit
  --tarfile TARFILE     Name of the tarfile that needs to be indexed
  --path PATH           Path to a directory containing tarfiles, all *.tar
                        files in dir will be indexed
  --extract EXTRACT     Comma separated list of the tarfile and files to be
                        extracted from that tarfile
  --outputpath OUTPUTPATH
                        Path to a directory where extracted files will be kept
  --bucketname BUCKETNAME
                        Name of the S3 bucket to get data from
  --configfile CONFIGFILE
                        Path to config File
```
## Sample Output - Indexing a tar file

```
python3 C:\Users\akulkarni\tools\index-tar\tar_tool.py --tarfile C:\Users\akulkarni\tools\index-tar\tfile.tar

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
```



## Sample Output - Extracting files from a tar

Note: The tool creates a directory with the name of tarfile under given output path, and extracts file(s) in it.

```
python3 C:\Users\akulkarni\tools\index-tar\tar_tool.py
--extract C:\Users\akulkarni\tools\index-tar\tfile.tar,one.txt,four.txt
--outputpath C:\Users\akulkarni\tools\index-tar\output
--bucket tar-exp0

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
```
## Tested by
* Sept 04, 2022: Arati Kulkarni (arati.kulkarni@seagate.com) on Windows 10

## Structure
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
