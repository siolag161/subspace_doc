.. highlight:: rst

Preprocessing 
==========================================================

Overview
------------------------------------
Pre-processing tasks involve

* Conversion from different formats into bedgraph using the tool provided by UCSC.
* Normalization of datasets
* Construction and generation of the data textfile

The idea is to be able to generate the final text files from datasets obtained from the Vital-IT BBCF plateform.

Data conversion
------------------------------------
The data obtained which provided by the Vital-IT BBCF plateform are first converted into ``.bedgraph`` format for the ease of processing. This process is achieved using the converter provided by the UCSC (mostly from .bw to .bedgraph). 

There is a simple python script called ``process_data.py`` which facilates the patch processing. In brief, it will find all the ``.bw`` files and convert it into ``.bedgraph`` file while keeping the same name. 

``.bedgraph`` is a csv-like file type and in our case is supposed to have 4 columns of which the first 3 related to coordonate information (chromosome, start, end) while the last one represent its average value over this portion). Therefore, it should look like:

+-------+---------+-------+-----------+
| chr   | start   | end   | val       |
+=======+=========+=======+===========+
| chr3R | 0       | 1000  |  0.5      |
+-------+---------+-------+-----------+
| chr3R | 2000    | 3000  |  0.8      |
+-------+---------+-------+-----------+
| ...   | ....    | ...   |  0.8      |
+-------+---------+-------+-----------+
| chr3R | 24000   | 25000 |  0.5      |
+-------+---------+-------+-----------+


otherwise it should be corrected.

Normalization
-------------------------------------------------------------
The normalization task consists of projecting the dataset onto equal-sized bins while computing the average over this portion. It can be done using a R script which requires as parameters: the ``chromosome`` (for now i cannot perform on the whole genome, we have to specifies the which chromosome that we want to limit the datas), the ``start`` and ``end`` base pair and the ``bin-size``.


Construction of output
-------------------------------------------------------------
The output files are also csv-like text files but have a slighly different format to the bedgraph file since it contains values of not only one but multiple tracks:

+-------+---------+-------+-----------+-----------+-----------+
| chr   | start   | end   |  track1   |    ...    |  track n  |
+=======+=========+=======+===========+===========+===========+
| chr3R | 0       | 1000  |    0.5    |    ...    |    0.5    |
+-------+---------+-------+-----------+-----------+-----------+
| ....  | ....    |  ...  |  ...      |    ...    |    ....   |
+-------+---------+-------+-----------+-----------+-----------+
| chr3R | 1000    | 2000  |  ...      |    ...    |    0.5    |
+-------+---------+-------+-----------+-----------+-----------+

There are many way to do this one of which is to perform first several normalization task on a set of tracks with which we want to work, then we join them together by their coorinates. Or we can append ``track$val`` one by one.



