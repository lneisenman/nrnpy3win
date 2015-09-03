=========================================================
Instructions for building Neuron for Python3.4 on Windows
=========================================================


These instructions are modified from the instructions given in `HowToMingw <http://www.neuron.yale.edu/hg/neuron/nrn/file/c92ef98299b0/howtomingw>`_.


Install Neuron release 1351
---------------------------

Install `Neuron release 1351 <http://www.neuron.yale.edu/ftp/neuron/versions/v7.4/nrn-7.4.x86_64-w64-mingw32-setup.exe>`_.
Make sure to choose the option for the DOS settings. If you have Python2.7
installed, you can test that python works correctly with the installation. In
case you need to undue what follows, backup C:\\nrn to C:\\nrnpy2


Install and configure MSYS2
---------------------------

Download and install `MSYS2 <http://sourceforge.net/projects/msys2/files/latest/download>`_.
If a terminal window doesn't automatically open at the end of the install, open
a MSYS2 shell using the Start Menu.

Update package directory with::

   pacman -Sy

Update core packages with::

   pacman --needed -S bash pacman pacman-mirrors msys2-runtime

If anything updated, restart the MSYS2 shell.

Update MSYS2 with::

   pacman -Su

Install packages using `pacman`::

   pacman -S base-devel
   pacman -S mingw-w64-x86_64-python3
   cp /mingw64/bin/2to3-3.4 /mingw64/bin/2to3


Download and uncompress `Carl Kleffner's Mingw build for Python 3 <https://bitbucket.org/carlkl/mingw-w64-for-python/downloads/mingwpy_amd64_vc100.7z>`_
and copy the resulting mingwpy folder into the C:\\msys64 folder.

Download and uncompress the corresponding `libpython <https://bitbucket.org/carlkl/mingw-w64-for-python/downloads/libpython-cp34-none-win_amd64.7z>`_
and copy the contents of libpython into the libs folder of your Python3.4 installation.

I'm not sure whether or not it is absolutely necessary, but I have Cython installed from
`Christoph Gohlke's binaries <http://www.lfd.uci.edu/~gohlke/pythonlibs/>`_ and have created
the distutils.cfg file in the C:\\Python34\\Lib\\distutils directory that contains::

   [build]
   compiler = mingw32


Build termcap
-------------

From the Start Menu, open a `MinGW-w64 Win64 Shell` 

Add the compiler to the path with::

   export PATH=/mingwpy/bin:$PATH
  
Download `termcap 1.3.1 <http://ftp.gnu.org/gnu/termcap/termcap-1.3.1.tar.gz>`_
After uncompressing:: 

   autoconf
   ./configure CC=x86_64-w64-mingw32-gcc --host=x86_64-w64-mingw32
   make
   x86_64-w64-mingw32-gcc.exe  -shared   -Wl,--out-implib,libtermcap.dll.a -o libtermcap.dll termcap.o tparam.o version.o
   cp termcap.h /mingwpy/x86_64-w64-mingw32/include
   cp libtermcap* /mingwpy/x86_64-w64-mingw32/lib


Build readline
--------------

Download `readline 6.2 <http://ftp.gnu.org/gnu/readline/readline-6.2.tar.gz>`_
After uncompressing::
 
   ./configure --prefix=`pwd` --disable-static CC=x86_64-w64-mingw32-gcc --host=x86_64-w64-mingw32
   make
   make install
   cp bin/* /mingwpy/x86_64-w64-mingw32/lib
   cp lib/* /mingwpy/x86_64-w64-mingw32/lib


Setup pthreads
--------------

Download `pthreads 20100604 <http://sourceforge.net/projects/mingw-w64/files/External%20binary%20packages%20%28Win64%20hosted%29/pthreads/pthreads-20100604.zip/download>`_
After uncompressing::

   cp pthreads-20100604/mingw64/pthreads-w64/x86_64-w64-mingw32/include/* /mingwpy/x86_64-w64-mingw32/include
   cp pthreads-20100604/mingw64/pthreads-w64/x86_64-w64-mingw32/lib/* /mingwpy/x86_64-w64-mingw32/lib


Build IV
--------

Download `IV-19 <http://www.neuron.yale.edu/ftp/neuron/versions/v7.4/v7.4.rel-1341/iv-19.tar.gz>`_
After uncompressing and renaming the directory to `iv`::

   ./configure --prefix=`pwd`
   make
   make install
  
  
Build NEURON
------------

Download the `Neuron Source <http://www.neuron.yale.edu/ftp/neuron/versions/v7.4/v7.4.rel-1341/nrn-7.4.rel-1351.tar.gz>`_
After uncompressing and renaming the directory to `nrn`, 
edit line 6612 in the file `nrn/configure` to read::

   npy_apiver=`$ac_nrn_python -c "import sys;sys.stdout.write(str(sys.api_version))"`

Replace `src/nrnpython/nrnpython.cpp` with `my edited version <https://github.com/lneisenman/nrnpy3win/blob/master/src/nrnpython/nrnpython.cpp>`_. 
This is necessary because the `PyFile_AsFile` macro does not exist in the
Python 3 C API.

Then build in 2 stages with the following commands::

   ./configure --prefix=`pwd` --without-iv --with-nmodl-only
   make
   ./configure --prefix=`pwd` --with-iv --without-nmodl --with-nrnpython=dynamic --host=x86_64-w64-mingw32 PYINCDIR=/c/Python34/include PYLIBDIR=/c/Python34/libs PYLIB='-L/c/Python34/libs -lpython34' PYLIBLINK='-L/c/Python34/libs -lpython34'
   make
   make mswin
  
The make mswin command will fail but will have created the directory C:\\marshalnrn64
From C:\\marshalnrn64\\nrn\\bin copy hocmodule.dll to C:\\nrn\\lib\\python\\neuron\\hoc.pyd
Replace the remianing python files in that folder with the corresponding files
from neuron compiled for python3 on a Linux system. I used Ubuntu 14.04 64 bit
running in Virtualbox. For some reason, 2to3 is not run on these files.
Similarly, cython is not run in the rxd folder although I'm not sure it is run
for Python2.7 either.

Copy all of the remaining files from C:\\marshalnrn64\\nrn\\bin into C:\\nrn\\bin 
  
You should be good to go.  
  
When running from a terminal window, I have had better luck with nrniv -python
as opposed to just starting with python because of problems with the value of
`h.neuronhome()`. However, when running scripts from Spyder, I have not had
problems.