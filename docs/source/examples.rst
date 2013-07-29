.. highlight:: rst

Usage
==========================================================


Preprocessing
---------------------------
* **conversion**: we first put the ``bigWig`` to be converted in a folder. ``process_data.py`` will output the converted into the output folder::
    python process_data.py -i <input path folder> -o <output path folder>

   there are two options to specify: the input path and the output path::
    python process_data.py -i ~/deme/data/input/ -o ~/deme/data/output

    ...converting /home/joules/deme/data/input/H3K27Me3_E8_12_GSM439446_merged.bw to /home/joules/deme/data/ouput/H3K27Me3_E8_12_GSM439446_merged.bedgraph ...
    ...converting /home/joules/deme/data/input/HP1_E8_16_GSM847702_merged.bw to /home/joules/deme/data/ouput/HP1_E8_16_GSM847702_merged.bedgraph ...
    ....


* **normalization**: For normalization-related task, there are two available scripts:

  * ``binAverage.BedGraph``: which takes the bedgraph file path, the limiting parameters (chr, start, end, binSize) and carries out the **binning** and **normalization**. It return a data.frame of the format specificied in the Preprocessing Section.

  * ``binAverage.BedGraph_Fillion``: which takes the Fillion file path, the track names (column names) that we want to extract from the origial dataset, the delimiting parameters (chr, start, end, binSize). It also carries out the **binning** and **normalization** and it return a data.frame of the format specificied in the Preprocessing Section.

  * ``import.fillion_patch``: which takes the path to the bedgraph directory, the path to the Fillion (or any file that looks like it), delimiting information, the set of track names from the Fillion we want to extract. It will find bedgraph files and  call the previous 2 sub-routines, then combine them together to form a unique data.frame R type::

      col_names=c("H1", "BEAF32", "CTCF","ChIP.H3K9me2.normH3")
      dataset = import.fillion_patch(bedgraph_path="~/deme/data/tester/",
                               fillion_path="./data/fillion_2010.csv",
                               chr="chr3R",startPos=0,endPos=3e6,binSize=10000,
                               sep="\t", colnames=colnames)
      
      write.table(dataset, ...)

* **Sexton**: a script to take the Sexton clustering CSV file and then converts it into a file whose format is compatible (meaning we can read it to build a referernce clustering in order to compare with other). It takes the path to the csv file, delimiting information and a list of indexes of the 4 dimensions that were employed during the Sexton cluster analysis w.r.t the set of dimensions that are included in our current dataset.::

    employed_list = c("H1", "HP1", "H3K4Me3","H3K4Me27")
    colname_idx = tools.IndexFromColnames(colnames(dataset), employed_list) # get the index of the
    colname_idx = colname_idx - 4 # index corrected, starting from 0 and offset by -3 (3 irrevelant coordinate columns, as we want to situatte them only in term of dimensions)

    sx = sexton.clustering_import(input_name="./data/sexton.csv",chr="chr3R",startPos=0,
                              endPos=1e6,binSize=1000, sep="\t",run=1, dims=colname_idx)
    write.table(sx, ...)



Post-processing
---------------------------

* **Exhautive-Kmeans**: list of parameters:
    * -h: help
    * -i: input 
    * -o: output
    * -d: size of projection (for instance 8 out of 15 available all of which will be considered as the full dimension by k-means)
    * -n: number of clusters (or k)
    * -p: number of parallel processes (7 by default)
    * -c: cluster or clusterings level of the format output: cluster:  if 1 and clustering: otherwise (0 by default)
    * Example::
	python km.py -i ./input/suite_15.csv -o ./output/suite_15_kmean_clusterings.csv -c 4 -n 4 -p 7 - c 1 

* **Pareto Frontier**: list of parameters:
    * -h: help
    * -i: input 
    * -o: output
    * -c: cluster or clusterings level of the format input: cluster:  if 1 and clustering: otherwise (0 by default)
    * Example::
	pareto.py -i ./output/suite_15_kmean_clusterings.csv -o ./output/pareto_suite_15_kmean_clusterings.csv -c 1 # as we specified 1 in the km.py

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







    
