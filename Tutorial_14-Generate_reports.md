This tutorial describes how to use the `swarm_generate_report` command to automatically process a log file and generate a PDF report. The first section of the report consists of a summary with some of the parameters of your experiment, such as number of messages sent, duration of the experiment, or the average number of neighbors per robot, among others. The following sections show plots with more detailed information of different aspects of the experiment.

## Prerequisites:

Prerequisites:

* Install `gnuplot` for generating plots:

```
#!python

sudo apt-get install gnuplot
```

* Install `pdflatex`:

```
#!python

sudo apt-get install texlive-latex-recommended
```


* Make sure that you are generating the python bindings when compiling the Swarm code. When running `make install` you should see quite a few python files installed in your target directory. If not, install the following package:

```
#!python
sudo apt-get install python-protobuf
```

* Make sure that you update your PYTHONPATH if you're not installing the Swarm files in a default location. E.g.:
 
``` 
#!python
 
export PYTHONPATH=/home/caguero/local/lib/python2.7/dist-packages/
```

## Step-by-step instructions


* Run a Swarm experiment with logging enabled. E.g.:

```
#!python

SWARM_LOG=1 gazebo worlds/complete_10.world
```

* Generate a report using `swarm_generate_report`. E.g.:

```
#!python

swarm_generate_report /home/caguero/.swarm/log/2015-11-18T15\:58\:44.954493/swarm.log /tmp/report1
```

The first argument is the path to the Swarm log file and the second parameter is the new directory where the reported will be created. This should generate three files:

* report.pdf : The report.
* swarm.csv: Text file with all relevant information grouped by columns.
* swarm_summary.tex: Summary of the report used to generate the PDF.

There are some fields in the report that may appear as `unknown`. This will be fixed in future pull requests that will probably require to add more fields to the log files.