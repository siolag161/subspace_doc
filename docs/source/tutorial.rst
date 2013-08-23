.. highlight:: rst

*********************************************************
Tutorial
*********************************************************

In the following section, we will perform the full clustering from the preprocessing until the post-processing.

Overvew
#############################
Suppose we have 4 tracks at our disposal: ``CP190, H3K4Me3, H3K27Me3, HP1`` all of which are in the `.bw` format. In addition, we have a bedgraph-like file containing the Sexton clustering and another one contains the Fillion tracks.

Preprocessing
#############################

Conversion
--------------
First we would want to convert our big wig ``.bw`` to begraph using the UCSD converter tool. Let say there's a folder containg all these file named `input` and we want to put the converted bedgraph files in an `output folder`. There's a ``helper`` python script to help automate the process called ``process_data.py`` ::

    C:\Users\pdt\Desktop\tutorial\preprocessing>python process_data.py -h
    [USAGE]: process_data.py -i <input path folder> -o <output path folder>

Use with the option ``-h`` give us the instruction of how to use it. So we have to provide the path to the input and output folders, respectively. So we enter::

    C:\Users\pdt\Desktop\tutorial\preprocessing>python process_data.py -i ./input -o ./ouput 
   
After a while, it outputs 4 ``.bw`` in the destination folder.

Binning and normalization
---------------------------
Next, we will construct our dataset composed of 4 Fillion tracks ``"H1", "BEAF32", "CTCF","ChIP.H3K9me2.normH3"`` and the 4 ``CP190, H3K4Me3, H3K27Me3, HP1`` from the converted bedgraph files. Suppose we have at disposal a ``bedgraph-like`` that contains information regarding these four tracks whose location is at ``"~/Desktop/tutorial/preprocessing/data/fillion_2010.csv``. We pass this path as value for ``ds_path`` and the name of columns as ``col_names``. Say moreover we have a folder containing 4 bedgraph ``.bedgraph`` data files whose location will be passed as ``bedgraph_path``. This function returns a data.frame which can be exported to a `csv-like` file. (See :ref:`preprocessing_listing` for more information)

In order to do so, we can use the helper function ``import.Patch_Process`` as following::

   
   # if we have a pre-processed dataset from which we want to extract some data to merge other bedgraph files.
   # let's say we want to extract H1 and BEAF32 from the fillion_2010.csv which is simply a bedgraph-like 
   # but contains multiple instead of just one track data.
   col_names=c("H1", "BEAF32", "CTCF","ChIP.H3K9me2.normH3") # fillion tracks to retain
   dataset = import.Patch_Process(bedgraph_path="~/Desktop/tutorial/preprocessing/data/tracks",
                               ds_path="~/Desktop/tutorial/preprocessing/data/fillion_2010.csv",
                               chr="chr3R",startPos=0,endPos=3e6,binSize=10000,
                               sep="\t", colnames=col_names)	
							   
   # otherwise we pass NULL as argument, colnames will be ingored
   #dataset = import.Patch_Process(bedgraph_path="~/Desktop/tutorial/preprocessing/data/tracks",
                               #ds_path=NULL,
                               #chr="chr3R",startPos=0,endPos=3e6,binSize=10000,
                               #sep="\t", colnames=col_names)	
	write.table(dataset, file="~/ws/stage/ds/output/datasets.csv", row.names=F)

						


Sexton reference
---------------------------
The third task in this process is to build the reference clustering file whose format is compatible with our pre-defined ones. The following piece of code will help accomplish that::


    # now we want to create a reference clustering file. supposed it is create 
    # using four tracks "H1", "HP1", "H3K4Me3","H3K4Me27" then we will need
    # the indexes of these 4 relatively to the tracks in the target columns

    employed_list = c("H1", "HP1", "H3K4Me3","H3K4Me27") #the 4 tracks that were used during the Sexton clustering process
    colname_idx = tools.IndexFromColnames(colnames(dataset), employed_list) # get the index of these 4 tracks among the columns in the dataset
    colname_idx = colname_idx - 4 # index corrected, starting from 0 and offset by -3 (3 coordinates)
    sx = sexton.clustering_import(input_name="./data/sexton.csv",chr="chr3R",startPos=0,
                                    endPos=1e6,binSize=1000, sep="\t",run=1, dims=colname_idx)

	write.table(dataset, file="~/ws/stage/ds/output/sexton_ds.csv", row.names=F)

The idea here is that we want to construct a record containing the clustering result found by Sexton in order to perform comparison with other clustering later. Apriori, we want to compare the objects and dimensions between clusterings. In specific, we compare the indexes of dimensions and objects, thus we have to obtain them which is the intention of the line ``2, 3``. Since in the experiment, ``HP1, H1, H3K4Me3, H3K4Me27`` were employed to perform the clustering, the use them in the line ``1``.


We then write to disk Sexton dataframe.

Clustering
#############################

In this section, we will rely on the KNIME plateform and its components available via the OpenSubspace clustering platform

KNIME installation
--------------------
Knime can be download freely via `this link <http://www.knime.org/downloads/overview>`_ , simply choose a version that corresponds to the plateform. After extracting the archive, we can beging using Knime.

Knime is built using the Eclipse plateform so the usage is rather similar. We can open a archive project using the ``File/Import/Archive File``. Here we open the project ``Spatiality`` that has been set up readily. Upon opening, we can see many components that werer added previously. In order to use the SubspaceClustering components, we have to install the `OpenSubspace plugins <http://dme.rwth-aachen.de/en/KnimeSC>`_ in Knime by going to ``Help->Install->Add`` and providing the source URL `http://dme.rwth-aachen.de/KnimeSCplugin`. We also need to install the Jython to execute the python in Knime. This one can be downloaded by choosing the ``Update Site plugin source``. 


KNIME execution
-----------------
After all plugins have been installed, we can start loading the dataset by providing the path in the ``FileReader Node`` (i.e ``"~/ws/stage/ds/output/datasets.csv"``). There are several parameters to change, the most important being the number of columns in the Python script. Specifically, we have to assign ``colNbr`` to the value of the number of track plus one. Here we have ``8`` so we assign the value 9 to its. ``colNbr = 9``. 

Other parameters to change include: the number of repeat for each non-deterministic algorithms and the range of values for each parameter's domain.

We repeat the same for each of the algorithm node and obtains the resulting clustering file which groups together all the results from different algorithms on different sets of parameters.


Exhautive K-means
-----------------
By contrast, the exhautive kmeans is a python module aims to perform the K-means clustering in an exhautive manner. More precisely, suppose we have a dataset which includes ``n`` tracks and we want to perform the kmeans on all the subspace projection of size ``k < n`` dimensions.

The list of all possible parameters:

    * -h: help
    * -i: input 
    * -o: output
    * -d: size of projection (for instance 8 out of 15 available all of which will be considered as the full dimension by k-means)
    * -n: number of clusters (or k)
    * -p: number of parallel processes (7 by default)
    * -c: cluster or clusterings level of the format output: cluster:  if 1 and clustering: otherwise (0 by default)

 
In this tutorial, we will feed this module with the csv datasets that were previously prepared::

	python km.py -i ~/ws/stage/ds/output/datasets.csv -o ~/ws/stage/ds/output/output_kmeans_4_4.csv -c 4 -n 4 -p 7 - c 1 

which means k-means clusterings using simultanously 7 processors with ``k=4`` on the subspace projections of size ``4``.

Post-processing
----------------------

* **Pareto Frontier**: as exlained in its own section, Pareto frontier is a module which, for each cluster, retains only the clusters that lie on the Pareto frontier in term of some dimensions. It accepts as input the clustering csv files and output the results of the same format::

	pareto.py -i ~/ws/stage/ds/output/output_kmeans_4_4.csv -o ./output/pareto_suite_15_kmean_clusterings.csv -c 1 # as we specified 1 in the km.py

so we can use either the clustering file generated by the exhautive k-means or the results of the clustering KNIME counterpart.

* **Scoring/Filtering/Ranking**: list of parameters:
    * -h: help
    * -i: input 
    * -o: output
    * -r: reference clustering source (shoule be at cluster level in order to be able construct its structure)
    * -c: cluster or clustering level of the output
    * -f: filtering or not (we may opt for measuring without performing the redundancy filtering)
    * -c: cluster or clusterings level of the format input: cluster:  if 1 and clustering: otherwise (0 by default)
    * -o: object threshold (for similarity compairison: we say they are similar in term of object if the score > this value)
    * -d: dimension threshold (also for similarity comparision)
    * Example::
	subspace_clustering.py -i ./output/suite_15_kmean_clusterings.csv -r ./input/sexton.csv -o ./scoring.csv -c 1 -f 1 -o 0.5 -d 0.5







    



