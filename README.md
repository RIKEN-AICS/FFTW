# FFTW patch for K computer   
This patchfile adds simd-support for the K computer to FFTW-3.3.6-pl2.  

## How to compile and install  
1.  Login to login node.
2.  Get fftw-3.3.6-pl2-fjsp.patch and fftw-3.3.6-pl2.tar.gz.  
3.  Apply patchfile.  
4.  Run `./configure` with each options.  
5.  Run `make`.
6.  Run '`make install`'.  
```
wget https://raw.githubusercontent.com/RIKEN-AICS/FFTW/master/fftw-3.3.6-pl2-fjsp.patch   
wget ftp://ftp.fftw.org/pub/fftw/fftw-3.3.6-pl2.tar.gz  
tar xfz fftw-3.3.6-pl2.tar.gz  
pushd fftw-3.3.6-pl2  
patch -p1 -i ../fftw-3.3.6-pl2-fjsp.patch  
PREFIX=/install/dir  
./configure \  
    --host=sparc64-unknown-linux-gnu \  
    --build=x86_64-unknown-linux-gnu \  
    CC="fccpx -Xg" \  
    CFLAGS="-Kfast,nosimd" \  
    F77="frtpx" \  
    FFLAGS="-Kfast,nosimd" \  
    MPICC="mpifccpx -Xg" \  
    MPIRUN="mpiexec" \  
    ac_cv_prog_f77_v="-###" \  
    --enable-fjsp \  
    --enable-fma \  
    --enable-openmp \  
    --enable-mpi \  
    --disable-dependency-tracking \  
    --prefix="${PREFIX}" \  
    --libdir="${PREFIX}/lib64"  
make  
make install  
```
### Run make check  
1.  Archive fftw-3.3.6-pl2 dicrectory after `make`.  
    e.g) `tar cfz fftw-3.3.6-pl2-build.tar.gz ./fftw-3.3.6-pl2`  
2.  Create make_check.sh file.  
cat make_check.sh
```
#!/bin/sh
#PJM --rsc-list "node=1"
#PJM --rsc-list "elapse=20:00"
#PJM --mpi "proc=4"
#PJM --stgin "fftw-3.3.6-pl2-build.tar.gz ./"
#PJM -s

source /work/system/Env_base

export FLIB_FASTOMP=FALSE
export FLIB_CNTL_BARRIER_ERR=FALSE

tar xfz fftw-3.3.6-pl2-build.tar.gz

pushd fftw-3.3.6-pl2
make check
popd
```
3.  Run '`pjsub make_check.sh`'.  
