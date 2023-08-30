---
title: 'HDF5 Installation'
date: 2023-08-31
permalink: /posts/2023/08/HDF5-Install
excerpt: "Guide for installing HDF5 with intel compilers"
tags:
  - installation guides
---

The Hierarchical Data Format version 5 [(HDF5)](https://www.hdfgroup.org/solutions/hdf5/), is an open source file format that supports large, complex, heterogeneous data. Many standard codes like Quantum Espresso, VASP, LAMMPS use HDF5 libraries to manage, process and store large heterogenous data.   
HDF5 is built for fast I/O processing and storage.  

Run the following bash script to install the HDF5 libraries and add the paths to your `.bashrc`

```
#!/usr/bin/env bash

# https://software.intel.com/en-us/articles/performance-tools-for-software-developers-building-hdf5-with-intel-compilers

# Run this script in the folder you want to install hdf5 in

mkdir HDF5; cd HDF5
mkdir Installation; cd Installation
DIR=$(pwd)
cd ../

## PREREQUISITES

# 1) szip 2.1.1

export CC=mpiicc
export CXX=mpiicpc
export FC=mpiifort
export CFLAGS='-O3 -xHost -ip -fPIC'
export CXXFLAGS='-O3 -xHost -ip -fPIC'
export FCFLAGS='-O3 -xHost -ip -fPIC'
wget https://support.hdfgroup.org/ftp/lib-external/szip/2.1.1/src/szip-2.1.1.tar.gz
tar -zxvf szip-2.1.1.tar.gz
cd Installation
mkdir szip-2.1.1
cd ../szip-2.1.1
./configure --prefix=$DIR/szip-2.1.1/
make
make check
make install
cd ../

# 2) zlib 1.3

wget https://zlib.net/zlib-1.3.tar.gz
tar -zxvf zlib-1.3.tar.gz
cd Installation
mkdir zlib-1.3
cd ../zlib-1.3 
./configure --prefix=$DIR/zlib-1.3 
make
make check
make install
cd ../

## ENV for HDF5

export CC=mpiicc
export F9X=mpiifort
export FC=mpiifort
export CXX=mpiicpc
wget https://support.hdfgroup.org/ftp/HDF5/releases/hdf5-1.14/hdf5-1.14.2/src/hdf5-1.14.2.tar.gz

### BUILD HDF5

njobs=$(( $(nproc) / 2 ))
tar -zxvf hdf5-1.14.2.tar.gz
cd Installation
mkdir hdf5-1.14.2
cd ../hdf5-1.14.2 
./configure --prefix=$DIR/hdf5-1.14.2 --with-szlib=$DIR/szip-2.1.1 --with-zlib=$DIR/zlib-1.3 \
            --enable-fortran --enable-shared --enable-hl --enable-parallel
make -j $njobs 
make -j $njobs check
make -j $njobs install

#### USAGE #####
# Add the following lines to ~/.bashrc
echo '# HDF5 load' >> ~/.bashrc
echo '# ---------' >> ~/.bashrc

echo 'export HDF5_INSTALL_DIR='$DIR >> ~/.bashrc
echo 'export LD_LIBRARY_PATH=$HDF5_INSTALL_DIR/szip-2.1.1/lib:$LD_LIBRARY_PATH' >> ~/.bashrc
echo 'export INCLUDE_DIR=$HDF5_INSTALL_DIR/szip-2.1.1/include:$INCLUDE_DIR' >> ~/.bashrc
echo 'export LD_RUN_PATH=$HDF5_INSTALL_DIR/szip-2.1.1/lib:$LD_RUN_PATH' >> ~/.bashrc

echo 'export LD_LIBRARY_PATH=$HDF5_INSTALL_DIR/zlib-1.3/lib:$LD_LIBRARY_PATH' >> ~/.bashrc
echo 'export INCLUDE_DIR=$HDF5_INSTALL_DIR/zlib-1.3/include:$INCLUDE_DIR' >> ~/.bashrc
echo 'export LD_RUN_PATH=$HDF5_INSTALL_DIR/zlib-1.3/lib:$LD_RUN_PATH' >> ~/.bashrc
echo 'export LIBRARY_PATH=$LIBRARY_PATH:$HDF5_INSTALL_DIR/zlib-1.3/lib' >> ~/.bashrc
echo 'export C_INCLUDE_PATH=$C_INCLUDE_PATH:$HDF5_INSTALL_DIR/zlib-1.3/include' >> ~/.bashrc
echo 'export CPLUS_INCLUDE_PATH=$C_PLUS_INCLUDE_PATH:$HDF5_INSTALL_DIR/zlib-1.3/include' >> ~/.bashrc

echo 'export HDF5_ROOT=$HDF5_INSTALL_DIR/hdf5-1.14.2' >> ~/.bashrc
echo 'export PATH=$HDF5_INSTALL_DIR/hdf5-1.14.2/bin:$PATH' >> ~/.bashrc
echo 'export LD_LIBRARY_PATH=$HDF5_INSTALL_DIR/hdf5-1.14.2/lib:$LD_LIBRARY_PATH' >> ~/.bashrc
echo 'export INCLUDE_DIR=$HDF5_INSTALL_DIR/hdf5-1.14.2/include:$INCLUDE_DIR' >> ~/.bashrc
echo 'export LD_RUN_PATH=$HDF5_INSTALL_DIR/hdf5-1.14.2/lib:$LD_RUN_PATH' >> ~/.bashrc
```
