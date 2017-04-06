# Linux Linear Algebra library optimization
I'm mostly following Hunseblog's post: https://hunseblog.wordpress.com/2014/09/15/installing-numpy-and-openblas/

### Prerequisites
`sudo apt-get install git python-dev gfortran` <br />
`pip3 install cython` (use `pip` or `pip3` depending on the Python version).

### Make OpenBLAS
Clone the repo and make (we will install it in `/opt`): <br />
`git clone https://github.com/xianyi/OpenBLAS` <br />
`cd OpenBLAS` <br />
`make FC=gfortran` <br />
`sudo make PREFIX=/opt/openblas install`

Link this new library by creating the file `/etc/ld.so.conf.d/openblas.conf` containing a single line with the path to the lib: `/opt/openblas/lib` and then run `sudo ldconfig` (it will internally use that file to make the link).

### Make Numpy
Clone the repo and make: <br />
`git clone https://github.com/numpy/numpy` <br />
`cd numpy`

Checkout the desire version: `git checkout v`.

Create a file `site.cfg` with the following data:
>[default] <br />
>include_dirs = /opt/openblas/include <br />
>library_dirs = /opt/openblas/lib <br />
>  <br />
>[openblas] <br />
>openblas_libs = openblas <br />
>library_dirs = /opt/openblas/lib <br />
>  <br />
>[lapack] <br />
>lapack_libs = openblas <br />
>library_dirs = /opt/openblas/lib

Proceed to make: <br />
`python3 setup.py config` <br />
`python3 setup.py build --fcompiler=gnu95` <br />
`python3 setup.py install`

### Make Scipy
Analogous to Numpy's procedure.

Clone the repo and make: <br />
`git clone https://github.com/scipy/scipy.git` <br />
`cd scipy`

Checkout the desire version: `git checkout v`.

Proceed to make: <br />
`python3 setup.py build` <br />
`python3 setup.py install`

### Test the installation
Check that numpy is correctly linked by running the python command: `np.show_config()`.

Run osdf's and hundeblog's test scripts. For reference, I got the following results:

_Hunseblog's numpy test_
>dotted two (1000,1000) matrices in 39.8 ms <br />
>dotted two (4000) vectors in 3.63 us <br />
>SVD of (2000,1000) matrix in 0.856 s <br />
>Eigendecomp of (1500,1500) matrix in 4.274 s

_Osdf's numpy test_
>dot: 0.023213736800244078 sec

_Osdf's scipy test_
>cholesky: 0.01407586119894404 sec <br />
>svd: 0.5167835889995331 sec
