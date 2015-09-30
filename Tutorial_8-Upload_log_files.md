This tutorial describes how to upload a log file to Amazon's S3.

Each team should have received S3 credentials. If not, contact your team lead or Nate (nate@osrfoundation.org).

## Directory structure

All logs should be uploaded to `s3://osrf-swarm/<team>/`, where `<team>` is one of `[byu, gatech, nps, upenn]`. The credentials provides upload access to only your team's directory.

## Install S3 command line tool

`sudo apt-get install s3cmd`

## Configure s3cmd

`s3cmd --configure`

Enter the Access Key and Secret Key. Leave other fields blank, do not test the setup, and then save the setup.

## List directory contents

```
s3cmd list s3://osrf-swarm/<team>
```

## Upload a log file

```
s3cmd sync <your_file> s3://osrf-swarm/<team>/
```
