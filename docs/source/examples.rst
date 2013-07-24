.. highlight:: rst

Usage
==========================================================


Preprocessing
---------------------------
* **conversion**: we first put the files to be converted into the input folder. by default, it will put the converted into the output folder. Both these two folders are direct descendants of the one containing `process_data.py` entry point::
    python process_data

  TODO: The next version will include the option to specify the source folder and the destination path

* **normalization**: Basically we need two scripts, the first one named ``binBedGraph.R`` neccessary for binning while the second one called `sexton_import.R` needed for importing Sexton as reference.


Dataset of 15 track::
    dataset = binAverage.BedGraph_Fillion(fname = "./data/fillion_2010.csv", 
                                      colnames = c("H1", "BEAF32", "CTCF","ChIP.H3K9me2.normH3"),
                                      chr="chr3R", startPos = 0, endPos = 27e6, binSize=10000) #importing the fillion tracks


    track_list = c("HP1","H3K4Me3","H3K27Me3","DNase","CBP", 
               "H3K9Ac", "K4me1", "PolII", "SuHw", "H3K27ac","CP190") #list of tracks to import
    for (track_name in track_list)
    {
        input_name = paste("~/deme/data/output/", track_name,".bedgraph", sep="") # construct the path for each path
	print(input_name) 
	track = binAverage.BedGraph(fname=input_name,
                                chr="chr3R", startPos = 0, endPos = 27e6, binSize=10000) #computing the track limiting on the chromosome 3R with bin size = 10k
	dataset[, track_name] = track$val # add new track as a variable in this data frame
    }


Sexton reference pseudo code for usage::
  
    sexton_tracks = sexton_track(path, chr, from, to, bin_size) # a bit like above
    sexton_dimensions_idx = computing_index (get indexes of the 4 dimensions used in sexton clustering)
    sexton_clusterings = sexton.clustering_generate(sexton_tracks, run, sexton_dimensions_idx)
    write.table(sexton_clusterings)

    TODO: clustering_id and other clustering and not only sexton


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
	python km.py -i ./test/suite_15.csv -o ./test/output_15_03_2014.csv -c 4 -n 4 -p 7 - c 1 

* **Pareto Frontier**: list of parameters:
    * -h: help
    * -i: input 
    * -o: output
    * -c: cluster or clusterings level of the format input: cluster:  if 1 and clustering: otherwise (0 by default)
    * Example::
	pareto.py -i ./test/output_15_03_2014.csv -o ./test/output_15_03_2014_pareto.csv -c 1 # as we specified 1 in the km.py

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
	subspace_clustering.py -i <inputfile> -r <reference-clustering path should be at the cluster_level> -o <outputfile> -c <output: 1 or cluster-level, otherwise it is clustering level> -f <Filtering or not> -t <in case of filtering is enabled, specifies which to output: the original (0), the filtered (1) or both (otherwise)>







    
