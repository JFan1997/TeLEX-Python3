
# TeLEX-Python3 

This is the Python3 version of TeLEX that all source code are updated to Python 3.12.
The original project is [TeLEX project](https://github.com/susmitjha/TeLEX.git) source code to Python3 version



Temporal Logic Extractor

==========================
Installation Instructions 
==========================

-----------------------------------------
1. Conda (Recommended and Updated for Python 3.12)
-----------------------------------------

1) Get hold of Conda: 
https://www.continuum.io/downloads#linux
http://conda.pydata.org/docs/install/full.html
> bash Anaconda-latest-Linux-x86_64.sh

2) Use the yml file in repository to setup environment for TeLEX
> conda env create telex
conda create -n telex python==3.12

3) Once the "telex" environment is present in your environment, you can activate it by:
> source activate telex

4) Install the required packages:
>pip install -r requirements.txt 

5) Add TeLEX home to your PYTHONPATH environment variable; illustrative example:
>(telex) jha@sjlinux1:~/projects/TeLEX$ export PYTHONPATH=$PWD

>(telex) jha@sjlinux1:~/projects/TeLEX$ echo $PYTHONPATH

>/home/jha/projects/TeLEX

6) Go to tests folder and run "python test_learn.py" to check if all tests pass and TeLEX is installed.
>(telex) jha@sjlinux1:~/projects/TeLEX$ cd tests/

>(telex) jha@sjlinux1:~/projects/TeLEX/tests$ python test_learn.py 

or run it as a pytest script, if you have pytest 

> (telex) jha@sjlinux1:~/projects/TeLEX/tests$ pytest test_learn.py 


-----------------------------------------
3. Direct Installation Notes 
-----------------------------------------

1) Prerequisite: Python and python-dev packages (Can use apt-get install on Ubuntu).

2) Python packages: numpy, scipy, pandas, parsimonious, singledispatch(python 2)/functools(python 3) (Can use pip install for these).

3) Use setup.py for installation or add TeLEX home to PYTHONPATH.

4) Run test scripts in tests to verify installation and modify these scripts as needed.

Troubleshooting: 
------------------

a) complain about six module :
sudo pip  install --upgrade six (six version might need upgrade, parsimonious depends on six)

b) linblopt import issue :
installation of bayesopt puts libnlopt.so in /usr/local/lib (or similar relative path if using prefix). You might have to do sudo ldconfig to rehash libraries.
Optional: Install BayesOpt tool with Python API wrapper

To fix:
--------

a) code assumes deterministic enumeration over lists in communicating with optimizer; this is probably not the case in python 3 or is likely to be removed soon for security issues. 


========================================================
Template Grammar
========================================================
``` 
formula = ( _ globally _) / ( _ future _ ) / ( _ until _ ) / ( _ expr _ ) / ( _ paren_formula _)
paren_formula = "(" _ formula _ ")"
globally = "G" interval formula
future = "F" interval formula
until = "U" interval "(" formula "," formula ")" 
interval = _ "[" _ bound  _ "," _ bound _ "]" _
expr = or / and / implies / npred / pred 
or = "(" _ formula _ "|" _ formula _ ")"
and = "(" _ formula _ "&" _ formula _ ")"
implies = "(" _ formula _ "->" _ formula _ ")"
npred = "!" _ formula 
pred = constraint / atom 
constraint =  term _ relop _ bound _
term = infix / var
infix = "{" _ term _ arithop _  term _ "}"
var = _ id _
atom = _ id _
bound = param / num 
param =  id "?" _ num ";" num _ 
id = ~r"[a-zA-z\d]+"
num = ~r"[\+\-]?\d*(\.\d+)?"
relop = ">=" / "<=" / "<" / ">" / "=="
arithop = "+" / "-" / "*" / "/"
_ = ~r"\s"*
```

Examples:

```
    'G[b? 1;6,  a? 1;9](x1 > 2)',
    'G[0,5] F[a? 0;3, b? 0;5] (x1 > 2)',
    'G[0,5] F[1, b? 0;5] (x1 > 2)',
    'G[0,5] F[a? 0;2 , 2] (x1 > 2)',
    'G[0,5] F[a? 1;2 , 3] (x1 > 2)',
    'G[0,9] ({ x1 - x2 } < a? -2;8 )',
    'G[0,9] ({ x2 - x1 } < a? -2;8 )',
    'U[0,a? 0;2] (x1 <= 10, x2<=5)'
```