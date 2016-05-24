# CUDA 7.5 installation guide on Ubuntu 15.10
----------------------------------------
## 1. Relevant documentation

- Stackoverflow: http://askubuntu.com/questions/693145/installing-cuda-7-5-toolkit-on-ubuntu-15-10

- [NVIDIA documentation]('http://developer.download.nvidia.com/compute/cuda/7.5/Prod/docs/sidebar/CUDA_Installation_Guide_Linux.pdf')


 ## 2. Pre-requisites

 To install CUDA you need:
 1. A NVIDIA graphic card (obviously)
 2. The corresponding graphic drivers installed
 3. The GCC compiler, version 4.9 ONLY
 4. Install the correct kernel headers

Point 2 will not be covered here. To know if a NVIDIA card is correctly installed
on your system enter following command.
 ```
 lspci | grep -i nvidia
 ```
If you do not see any settings, update the PCI hardware database that Linux maintains
by entering `update-pciids` (generally found in `/sbin`) at the command line and rerun
the previous `lspci` command.

If this does not solve the issue, look into installing the correct driver. I have
never managed to do it myself but look into **Bumblebee**.

For point 3, the Stackoverflow post was very useful. Here his what you need to
enter in your terminal, I will explain line by line afterward:
```
sudo apt-get install gcc-4.9 g++-4.9
sudo mkdir /opt/compiler_cuda
sudo ln -s /usr/bin/gcc-4.9 /opt/compiler_cuda/gcc
sudo ln -s /usr/bin/g++-4.9 /opt/compiler_cuda/g++
sudo ln -s /opt/compiler_cuda/gcc /opt/compiler_cuda/cc
```

Basically, we install GCC4.9, then prepare a folder with symlinks to both gcc and
g++ that will be further referenced in the config file of CUDA. We will come back
on that later.

For point 4, you just have to type:
```
sudo apt-get install linux-headers-$(uname -r)
sudo apt-get autoremove
```

## 3. Download CUDA

Go to [NVIDIA developer website]('https://developer.nvidia.com/cuda-downloads') and download
the correct package. I used the local `deb` installer. Follow Linux -> x86_64 -> Ubuntu -> 15.04 -> deb(local).

For those wondering if we can use the installer for 15.04, part of the answer lies in the Stackoverflow.

*"It isn't dangerous to use the CUDA toolkit from 15.04." - Goddard*

*"From what I understand, all of Nvidia's drivers are backwards compatible with all versions of CUDA runtime libraries. This is so newer drivers do not break older CUDA code"*

## 4. Install CUDA (local deb)

You can verify your download by doing:
```
md5sum <file>
```

Then run the following commands:
```
sudo dpkg -i cuda-repo-<distro>_<version>_<architecture>.deb
sudo apt-get update
sudo apt-get install cuda
```

**Reboot the computer** on completion of the installation.

## 5. Set-up the GCC compiler

As prepared earlier on, we now need to link CUDA to the GCC 4.9 compiler. To do so,
append the following line to the `nvcc.config` file which you can find at `usr/local/cuda/bin/nvcc.config`:
```
compiler-bindir = /opt/compiler_cuda
```

Now the compiler is all set.

## 6. Set-up the environment PATH and LD_LIBRARY_PATH

You need to add to your `.bashrc` (usually append, so put these lines at the end of
your `.bashrc` if you are not familiar with the bashrc, bashrc file can be found in
your home folder).

```
export CUDA_HOME=/usr/local/cuda-7.5
export LD_LIBRARY_PATH=${CUDA_HOME}/lib64

PATH=${CUDA_HOME}/bin:${PATH}
export PATH
```

## 7. Profit

You should be all set now, enjoy the speed.
