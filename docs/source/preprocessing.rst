.. highlight:: rst

Preprocessing 
==========================================================

Overview
------------------------------------
Pre-processing tasks involve

* Conversion from different formats into bedgraph using the tool provided by UCSC.
* Normalization of datasets
* Construction and generation of the data textfile


Data conversion
------------------------------------
The data obtained which provided by the Vital-IT BBCF plateform are first converted into ``.bedgraph`` format for the ease of processing. This process is achieved using the converter provided by the UCSC (mostly from .bw to .bedgraph). 

There is a simple python script called ``process_data.py`` which facilates the patch processing. In brief, it will find all the ``.bw`` files and convert it into ``.bedgraph`` file while keeping the same name. 
