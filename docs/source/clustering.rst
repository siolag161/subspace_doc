.. highlight:: rst

Clustering
==========================================================



Overview
-------------------------------------------------------------
The clustering part is handled mostly by the Knime plateform using the components provided by the `OpenSubspace Project http://dme.rwth-aachen.de/en/OpenSubspace>`_. It take the files of a specific csv-like format as input and perform the subspace cluster analysis using multiple algorithms on a set of different parameters,

By definition, the result of a run of a subspace clustering algorithm is a list of pair :math:`(O_{i},D_{i})` objects and dimensions associated. We will write all relevant information onto disk, including: the clustering_id, cluster objects, cluster dimensions, paremeters used, run_id, algorithm employed.


Input
-------------------------------------------------------------
The input for the clusteing is simply the output of the :ref:`_pre_processing_format` task i.e. csv-like text-files containing a set of tracks.

Output
-------------------------------------------------------------
Regardless of algorithms employed, the output will be the same as mentioned earlier. The format is csv-like where each line depicts a cluster definion (objects, dimensions), the identity of the clustering to which it belongs, the algortihm and parameters used, the nun identity (as many stochastic algorithms might give different result at each run, so we might have to perform several time on the same parameter).

The objects are a list of indexes of the objects found in this cluster, seperated by a colon ','. The same goes for dimensions.

+-------+---------+-------+-----------+-----------+-----------+
| algorithm  | parameter   |  run_id | clustering_id  |  objects   |   dimensions  |
+=======+=========+=======+===========+===========+===========+


Algorithms
-------------------------------------------------------------
List of algorithms employed:
* Subclu
* ...

