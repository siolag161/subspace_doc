.. highlight:: rst

.. _preprocessing_label:
Preprocessing 
==========================================================

Overview
------------------------------------
Data pre-processing is the first step in our work-flow in order to uniformize and normalize the data format from different sources to the unique one.

* Conversion from different formats into bedgraph using the tool provided by UCSD.
* Normalization of datasets: the data are normalize using the log-quantile
* Construction and generation of the dataset: combining differents tracks to form a unique data file under a certain format.


Data conversion
------------------------------------
The data obtained which provided by the Vital-IT BBCF plateform are first converted into ``.bedgraph`` format for the ease of processing. This process is achieved using the converter provided by the UCSD (mostly from .bw to .bedgraph). 

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

Common issues
------------------------------------
Some troubles that one might encounter during this phase includes: 
* Naming convention: the naming of chromosome might differ from the default pattern ``chrX`` (it might be only `X` instead): for instance in R we can use ``sub("^", "chr", dat$V1)``.
* Seperator: by default, the seperator equals ``,`` but it might not be the case in reality.


Normalization
-------------------------------------------------------------
The normalization task consists of projecting the dataset onto equal-sized bins while computing the average over this portion. It can be done using a R script which requires as parameters: the ``chromosome`` (for now i cannot perform on the whole genome, we have to specifies the which chromosome that we want to limit the datas), the ``start`` and ``end`` base pair and the ``bin-size``.

.. _pre_processing_format_label:
Construction of output
-------------------------------------------------------------
Our dataset can possibly consist not only one but multiple tracks, thus we will have to combine these track in order to form a unique, matrix-like 
dataset. We can consider this datase as the join of all these tracks by their common genomic coordinates. It might look as described by :ref:` _preprocessing_construction_output_table` :

.. _preprocessing_construction_output_table:
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

.. _preprocessing_listing:

Listing
--------------------------------
As aformentioned, this steps involves several R scripts, some of which are:
 
* bin_average.R: contains a set of procedure enabling the normalization of data of which the three most important are::
   
    binAverage.BedGraph = function(fname, chr="chr3R", startPos = 12e6, 
                                       endPos = 14e6, binSize=1000, sep="\t")

    # fname: path to the bedgraph data file
    # chr: chromosome to which we are limited
    # startPos, end Post: the position of interest (in base pair) on the chromosome
    # binSize: length (in base pair) we want to obtain
    # sep: deliminater used by the bedgraph data file

which takes a bedgraph data file and outputs a data.frame holding the normalized binned of the desired portion. The next one one aims to facilitate the patch processing multiple files/datasets at once. Indeed, suppose we want to obtain a dataset and we have a folder containing a set of bedgraph files and another dataset. The following procedure will try to construct a new dataset by extracting data from the existing dataset while adding from bedgraph files. If we just want to construct from the bedgraph files, just leave the ``ds_path`` as ``NULL``::

    import.Patch_Process = function(bedgraph_path, ds_path, colnames,
                                chr="chr3R",startPos=0,endPos=3e6,binSize=10000,
                                sep="\t")
	## takes  requires a path to a folder containing bedgraph textfiles (the name 
	## of the file will be used as columns) and a path to another dataset
	## to merge. colnames are sets of column names to be extracted
	
	# fname: path to the bedgraph data files
	# ds_path: path to a dataset consisting of multiple tracks
	# colnames: set of columns to extract from the file in ds_path
	
    # chr: chromosome to which we are limited
    # startPos, end Post: the position of interest (in base pair) on the chromosome
    # binSize: length (in base pair) we want to obtain
    # sep: deliminater used by the bedgraph data file	  


* usage.R: a script to demonstrate how to use these procedures::

    # if we have a pre-processed dataset from which we want to extract some data to merge other bedgraph files.
    # let's say we want to extract H1 and BEAF32 from the fillion_2010.csv which is simply a bedgraph-like 
    # but contains multiple instead of just one track data.
    col_names=c("H1", "BEAF32")
    dataset = import.Patch_Process(bedgraph_path="~/Desktop/tutorial/preprocessing/data/tracks",
                               ds_path="~/Desktop/tutorial/preprocessing/data/fillion_2010.csv",
                               chr="chr3R",startPos=0,endPos=3e6,binSize=10000,
                               sep="\t", colnames=col_names)	
							   
    # otherwise we pass NULL as argument, colnames will be ingored
    dataset = import.Patch_Process(bedgraph_path="~/Desktop/tutorial/preprocessing/data/tracks",
                               ds_path=NULL,
                               chr="chr3R",startPos=0,endPos=3e6,binSize=10000,
                               sep="\t", colnames=col_names)	
							   
    # so we can do something like ds1 = import.Patch_Process(bed_graph, ds_path = NULL)
    # and another one ds2 = import.Patch_Process(bed_graph, path_to_ds1) to merge with the first ds 
							   

    # now we want to create a reference clustering file. supposed it is create 
    # using four tracks "H1", "HP1", "H3K4Me3","H3K4Me27" then we will need
    # the indexes of these 4 relatively to the tracks in the target columns

    employed_list = c("H1", "HP1", "H3K4Me3","H3K4Me27") # 4 tracks used by the reference clustering
    colname_idx = tools.IndexFromColnames(colnames(dataset), employed_list) # get the index of the
    colname_idx = colname_idx - 4 # index corrected, starting from 0 and offset by -3 (3 coordinates)

    # create the sexton reference clustering
    sx = sexton.clustering_import(input_name="./data/sexton.csv",chr="chr3R",startPos=0,
                              endPos=1e6,binSize=1000, sep="\t",run=1, dims=colname_idx)






