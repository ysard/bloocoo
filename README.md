# Bloocoo user manual

Gaetan Benoit, Claire Lemaitre, Dominique Lavenier, Guillaume Rizk.		  		  
					  Dec 2015


								
#LICENSE
	*  GNU AFFERO GENERAL PUBLIC LICENSE
	*  Copyright (C) 2015 INRIA
	see the LICENSE file
	
#INSTALL

CMake is required to compile bloocoo (http://www.cmake.org/cmake/resources/software.html)

See the INSTALL file.

NOTE: If you retrieved a source archive, you can compile the project with 'sh INSTALL’

Compilation was tested with gcc and g++ version>=4.5 and clang version>=4.1
	

# User manual	 
								
## Description

Bloocoo is a kmer-spectrum based  read error corrector. In a first pass, all  k-mers are counted, then  k-mers more abundant than a given threshold are kept, i.e. “solid k-mers”.

Correction is then performed by scanning  k-mers of a read. For example, a single isolated error generates a gap of k non solid k-mers making the detection of its exact location easy. Correction is made by trying the three different possible nucleotides at the error site, and checking if corresponding k-mers are in the set of solid k-mers.

When several close errors occurs, the pattern is more complex, errors are corrected via a vote algorithm similar to the one in the Musket software (http://musket.sourceforge.net/).

What makes Bloocoo different is the k-mer counting stage and the way solid k-mers are stored in memory. k-mer counting is conducted via the DSK algorithm included in the GATB library, which requires constant-memory. Solid k-mers are stored in a Bloom filter which is fast and memory-efficient : we use only 11 bits of memory per solid k-mers. Therefore,  correction of a whole human genome sequencing read set needs only 4GB of memory.


## Usage

A typical command line is:

    Bloocoo -file reads.fasta -kmer-size 27  -abundance 4

There is 1 mandatory argument:

    -file : the read file name, can be fasta, fastq, gzipped or not.

Two important arguments:

    -kmer-size : the k-mer size (typically ~31)
    -abundance-min : the minimal abundance threshold defining solid k-mers (typically  between 3 and 6, but depends on the read depth, you can also use 'auto' and it is automatically inferred from the data)

Additional useful options :

    -nb-cores  : number of threads used
    -high-recall  :  correct more errors but can also introduce more mistakes
    -slow : slower modes with more pass, but better correction
    -high-precision :  correct safely, correct less errors but introduce less mistakes
    -ion : (experimental) mode for correcting indels present in  ion torrent reads


## Examples

    ./Bloocoo -file reads.fasta
    -> generates the file reads_corrected.fasta
 

Note : 
In order to use k values larger than 31, recompilation is necessary (for the moment, this will be improved in next versions).

In the sequence of commands given in the INSTALL file, change the command: 

    cmake ..

by 

    cmake -DKSIZE_LIST="64" ..

this will allow to use k<63

For larger k, change the value such that it is a multiple of 32
