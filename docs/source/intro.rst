.. highlight:: rst

.. _introduction_label:

Introduction 
==========================================================

This document is meant to explain how to use the code that accompanies the subspace clustering project (SuClust) to perform the related tasks. For more detailled information, one can refer to the reports (:download:`thesis <static/internship_report_master_2013.pdf>` or :download:`slides <static/presentation_master_2013_final.pdf>`).
 
Overview
------------------------------------
The project consists of 3 main parts, each of which corresponds to a phase in the workflow: 

* :doc:`preprocessing`: composed of some scripts, mostly written in R to handle dataset processing (cleaning, normalization...). 
* :doc:`clustering`: implementation of some subspace clustering algorithms (in Knime, python...). They perform the cluster analysis on the data provided by the previous process and output results in textfiles of a specific format.
* :doc:`postprocessing`: python programs to perform these following tasks: redundancy filtering, measure scoring/ranking on files generated by clustering files. 

Source code
------------------------------------
The updated source code repository is hosted on github whose link is: `subspace_clustering <https://github.com/siolag161/subspace_clustering>`_.


Prerequisites
------------------------------------
To get started with SuClust, one must have the following installed:

* For executing Python scripts (pre-processing, post-processing, clustering):
 * **Python**: `Official Python tutorial`_ : 2.7 version is recommended.
 * **Numpy**: `Official Numpy tutorial`_ : some of the algorithms are implemented based on the array structures of Numpy.
 * **Scipy**: `Official Scipy tutorial`_ : some modules of Scipy are also used.

* For performing cluster analysis with OpenSubspace (clustering):
 * **Knime**: `Official Knime-OpenSubspace tutorial`_ : a modified version with OpenSubspace integrated.

* For running R scripts (pre-processing):
 * **R**: `Official R tutorial`_ 

.. _Official Python tutorial: http://docs.python.org/tutorial/
.. _Official Numpy tutorial: https://pypi.python.org/pypi/numpy
.. _Official Scipy tutorial: http://www.scipy.org/
.. _Official Knime-OpenSubspace tutorial: http://dme.rwth-aachen.de/en/KnimeSC
.. _Official R tutorial: http://www.r-project.org/other-docs.html






