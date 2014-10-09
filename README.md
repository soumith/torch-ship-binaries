torch-ship-binaries
===================

A page describing how to ship torch binaries without sharing the source code of your scripts.

1) Create a self-contained torch install: 
```bash
git clone https://github.com/soumith/torch-distro.git
cd torch-distro
install.sh
```
(you can add and remove packages from install.sh, i've added what I usually need all the time).

2) Convert your lua scripts into bytecode:
```
luajit -b [script.lua] [script.out]
```
More doc here: http://luajit.org/running.html

3) Place the byte-code scripts in torch-distro/myscripts

4) Create a shell script in the root of torch-distro that sets the proper environment for your end-user, and passes the commandline options into your main lua script: 
torch-distro/run.sh
```bash
#!/bin/bash
currdir=`dirname $0`
currdir=$(cd "$currdir" && pwd)
export PATH=$currdir/install/bin/$PATH
export LD_LIBRARY_PATH=$currdir/install/lib/$LD_LIBRARY_PATH
export DYLD_LIBRARY_PATH=$currdir/install/lib/$DYLD_LIBRARY_PATH
$currdir/install/bin/luajit myscripts/main.lua $*
```

5) Zip it and ship it, ask your thirdparty to unzip and start the program with "run.sh"
All the commandline options will be passed to the entry lua script, so you can have command-line options using torch.CmdLine or penlight etc.
