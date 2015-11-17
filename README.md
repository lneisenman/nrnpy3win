# nrnpy3win
Neuron compiled to run with 64 bit Python3.4 on Windows 64 bit

This is a set of instructions for how to compile [Neuron](http://www.neuron.yale.edu/neuron/)
for Python3.4 on Windows. It also includes a compressed version that can be
directly installed as described below. This is **EXPERIMENTAL** and should be
assumed to be **bug ridden** until proven otherwise.


##Installation

Download and install [Neuron release 1370](http://www.neuron.yale.edu/ftp/neuron/versions/v7.4/nrn-7.4.x86_64-w64-mingw32-setup.exe).
Make sure to choose the option for the DOS settings. If you have Python2.7
installed, you can test that python works correctly with the installation.

Rename `C:\nrn` as `C:\nrnpy2`

Download and uncompress [my version of the nrn folder](https://github.com/lneisenman/nrnpy3win/releases/download/v0.1.0alpha/nrn.7z)
and place at `C:\nrn`. You should now be good to go. When running from a terminal
window, I have had better luck with nrniv -python as opposed to just starting
with python because of problems with the value of `h.neuronhome()`. However,
when running scripts from [Spyder](https://pythonhosted.org/spyder/),
I have not had problems.


Detailed instructions describing what I did are available [here](https://github.com/lneisenman/nrnpy3win/blob/master/Instructions.rst).

Although Python3.5 will be released shortly, I don't think it will be possible
to replicate this process until there is a way to [use Mingw to build python
modules that are compatible with the new version of MS Visual C](http://stevedower.id.au/blog/building-for-python-3-5/).
More info [here](http://bugs.python.org/issue4709#msg243625).
