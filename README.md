# mummergpu

This project contains a fork of [MUMmerGPU-2.0](https://sourceforge.net/p/mummergpu/wiki/MUMmerGPU/) that compiles out of the box.

MUMMerGPU-2.0 is an excellent open source GPU-based pairwise sequence alignment program. It was last updated in 2010. 
Currently, compilation errors prevent the MUMmerGPU-2.0 source code distribution from being immediately compiled and 
run on 64-bit Linux systems using current C++ compilers.

This project contains a slightly modified copy of MUMMerGPU-2.0 that compiles correctly using g++ without requiring 
further changes to the source code.

This project avoids the following messages:

* `mummergpu.cu(468): error: argument of type "unsigned int *" is incompatible with parameter of type "size_t *"`
* `mummergpu_gold.cpp:(.text+0x0): multiple definition of 'getRef(int, char*)'`
* `/usr/bin/ld: cannot find -lcudart`
* `common.cu:132:5: error: uint32_t does not name a type`
* `suffix-tree.cpp:47:39: error: strncpy was not declared in this scope`...
* `PoolMalloc.cpp:94:7: error: stderr was not declared in this scope`...
* `mummergpu.cu:478: warning: converting to int from float`...

All of my modifications can be viewed in the [commit history](https://github.com/rmtheis/mummergpu/commits/master).

## Original Readme

See [mummergpu-2.0/README](mummergpu-2.0/README).

## Installing on Amazon EC2

Launch instance ami-aa30c7c3 (CentOS 5.5 GPU HVM AMI) using instance Cluster GPU cg1.4xlarge, 22GB.

Connect to your instance:

    ssh -i path_to_public_keyfile.pem root@ec2-00-00-000-000.compute-1.amazonaws.com

Update the kernel:

    yum update
    reboot
    uname -a

Update the Nvidia driver to v295.41 (or get a newer version from [here](http://developer.nvidia.com/cuda-downloads)):

(_Ignore the error saying_ `ERROR: File '/usr/lib64/xorg/modules/extensions/libglx.so' is not a symbolic link.`)

    wget http://developer.download.nvidia.com/compute/cuda/4_2/rel/drivers/devdriver_4.2_linux_64_295.41.run
    chmod +x devdriver_4.2_linux_64_295.41.run
    ./devdriver_4.2_linux_64_295.41.run 
    reboot
    /usr/bin/nvidia-smi -q -a

Update the CUDA Toolkit to v4.2.9 (or get a newer version from [here](http://developer.nvidia.com/cuda-downloads) for Red Hat 5.5):

    wget http://developer.download.nvidia.com/compute/cuda/4_2/rel/toolkit/cudatoolkit_4.2.9_linux_64_rhel5.5.run
    chmod +x cudatoolkit_4.2.9_linux_64_rhel5.5.run
    ./cudatoolkit_4.2.9_linux_64_rhel5.5.run
    /usr/bin/nvidia-smi -q -a

Install git:

    rpm -Uvh http://repo.webtatic.com/yum/centos/5/latest.rpm
    yum install --enablerepo=webtatic --disableexcludes=main git-all

Install g++:

    yum install gcc-c++

_At this point, continue with "Installling Locally" below._

## Installing Locally

    git clone git://github.com/rmtheis/mummergpu
    cd mummergpu/mummergpu-2.0/src
    make
    export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda/lib64
    export PATH=$PATH:/root/mummergpu/mummergpu-2.0/bin/release
    mummergpu
    mummergpu ../../data/shortref.fa ../../data/shortqry.fa

## License

[Artistic License 1.0](COPYING)
