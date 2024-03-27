# Install ISCE2 on Caltech HPC

The GPU nodes in Caltech hpc cluster currently have RHEL9 installed. There are 4 P100 cards installed in each gpu node (some are V100, A100). 

1. Install conda/mamba build on Python 3
   
According to the [Mamba Installation Guide](https://mamba.readthedocs.io/en/latest/installation/mamba-installation.html), it is recommended that you start with a new fresh Python system, e.g., Miniforge. 

Here, we download a x86_64 build of [Miniforge](https://github.com/conda-forge/miniforge), and install it to ``$HOME/miniforge`` directory. 

    curl -OL https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-Linux-x86_64.sh 
    sh ./Miniforge3-Linux-x86_64.sh

2.  Create a virtual environment and required packages

It is recommended that we install ISCE2 to a dedicated venv to avoid packages conflicts with other software. 

    mamba create -n isce2
    mamba activate isce2
    mamba install git cython gdal h5py libgdal pytest numpy fftw scipy opencv pybind11 shapely opencv

3. Download and Compile ISCE2

First we load the CUDA compiler, 

    module load cuda 
    
Remember also, when you run isce2, or in the slurm script, please add this line first. 

Download the most recent version or a release version from [ISCE2 github page](https://github.com/isce-framework/isce2),

    mkdir -p $HOME/tools/src/
    cd $HOME/tools/src/
    git pull https://github.com/isce-framework/isce2.git
    
and use CMake to compile 

    cd $HOME/tools/src/isce2
    # create a build directory
    mkdir build  && cd build
    # use a symbolic link instead of specify -DPYTHON_MODULE_DIR=lib/python3.xx/site-packages
    ln -sf `python3 -c 'import site; print(site.getsitepackages()[0])'` $CONDA_PREFIX/packages  
    # run cmake config
    cmake .. -DCMAKE_INSTALL_PREFIX=$CONDA_PREFIX \
         -DCMAKE_CUDA_ARCHITECTURES=60 \
         -DCMAKE_PREFIX_PATH=${CONDA_PREFIX} \
         -DCMAKE_BUILD_TYPE=Release 
    # compile and install 
    make -j && make install

4. Examples of slurm script

TBD

    
