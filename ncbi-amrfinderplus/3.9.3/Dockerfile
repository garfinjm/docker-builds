FROM ubuntu:bionic

# For easy upgrade later. ARG variables only persist during build time
ARG AMRFINDER_VER="3.9.3"
ARG BLAST_VER="2.10.0"
ARG HMMER_VER="3.3"

LABEL base.image="ubuntu:bionic"
LABEL dockerfile.version="1"
LABEL software="NCBI AMRFinderPlus"
LABEL software.version="3.9.3"
LABEL description="NCBI resistance gene detection tool"
LABEL website="https://github.com/ncbi/amr"
LABEL maintainer1="Kelsey Florek"
LABEL maintainer1.email="kelsey.florek@slh.wisc.edu"
LABEL maintainer2="Curtis Kapsak"
LABEL maintainer2.email="pjx8@cdc.gov"

# install dependencies, clean up apt garbage
RUN apt-get update && apt-get install -y \
 wget \ 
 curl \
 make \
 g++ && \
 apt-get autoclean && \
 rm -rf /var/lib/apt/lists/*

# ncbi-blast+; make /data directory
RUN wget ftp://ftp.ncbi.nlm.nih.gov/blast/executables/blast+/${BLAST_VER}/ncbi-blast-${BLAST_VER}+-x64-linux.tar.gz && \
 tar zxf ncbi-blast-${BLAST_VER}+-x64-linux.tar.gz && \
 rm ncbi-blast-${BLAST_VER}+-x64-linux.tar.gz && \
 mkdir /data 

# hmmer 3.3 since amrfinder requires 3.2 or higher (apt version is 3.1)
# apt dependencies: make, g++
RUN wget http://eddylab.org/software/hmmer/hmmer-${HMMER_VER}.tar.gz && \
 tar xzf hmmer-${HMMER_VER}.tar.gz && \
 rm hmmer-${HMMER_VER}.tar.gz && \
 cd hmmer-${HMMER_VER} && \
 ./configure && \
 make && \
 make install

# ncbi AMRFinderPlus
# apt dependencies: curl
RUN mkdir amrfinder && cd /amrfinder && \
 wget https://github.com/ncbi/amr/releases/download/amrfinder_v${AMRFINDER_VER}/amrfinder_binaries_v${AMRFINDER_VER}.tar.gz && \
 tar zxf amrfinder_binaries_v${AMRFINDER_VER}.tar.gz && \
 rm amrfinder_binaries_v${AMRFINDER_VER}.tar.gz

# set PATH and locale settings for singularity compatibiliity
ENV PATH="${PATH}:/ncbi-blast-${BLAST_VER}+/bin:\
/amrfinder"\
 LC_ALL=C

# download databases
RUN cd /amrfinder && amrfinder -u

WORKDIR /data
