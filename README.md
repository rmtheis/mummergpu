#MUMmerGPU
* * *

This project contains a fork of [MUMmerGPU-2.0](http://sourceforge.net/apps/mediawiki/mummergpu/index.php?title=MUMmerGPU)
 that compiles out of the box.

I've made several small changes to the code to get it to build, fixing the following messages:

* `mummergpu.cu(468): error: argument of type "unsigned int *" is incompatible with parameter of type "size_t *"`
* `mummergpu_gold.cpp:(.text+0x0): multiple definition of `getRef(int, char*)'`
* `/usr/bin/ld: cannot find -lcudart`
* `common.cu:132:5: error: uint32_t does not name a type`
* `suffix-tree.cpp:47:39: error: strncpy was not declared in this scope`...
* `PoolMalloc.cpp:94:7: error: stderr was not declared in this scope`...
* `mummergpu.cu:478: warning: converting to int from float`...

All my changes can be viewed in the [commit history](https://github.com/rmtheis/intron-polymorphism/commits/master).

## Installing on Amazon EC2

Launch instance ami-aa30c7c3 (amazon/EC2 CentOS 5.5 GPU HVM AMI) using instance Cluster GPU cg1.4xlarge, 22GB ($2.10/hr).

Connect to your instance:

    ssh -i path_to_my_public_keyfile.pem root@ec2-00-00-000-000.compute-1.amazonaws.com

Update the kernel:

    yum update
    reboot
    uname -a

Update the Nvidia driver:

    wget http://developer.download.nvidia.com/compute/cuda/4_2/rel/drivers/devdriver_4.2_linux_64_295.41.run
    chmod +x devdriver_4.2_linux_64_295.41.run
    ./devdriver_4.2_linux_64_295.41.run 
(Ignore the error saying `ERROR: File '/usr/lib64/xorg/modules/extensions/libglx.so' is not a symbolic link.`)
    reboot
    /usr/bin/nvidia-smi -q -a

(or get a newer version from [here](http://developer.nvidia.com/cuda-downloads))

Update the CUDA toolkit:

    wget http://developer.download.nvidia.com/compute/cuda/4_2/rel/toolkit/cudatoolkit_4.2.9_linux_64_rhel5.5.run
    chmod +x cudatoolkit_4.2.9_linux_64_rhel5.5.run
    ./cudatoolkit_4.2.9_linux_64_rhel5.5.run
    /usr/bin/nvidia-smi -q -a

(or get a newer version from [here](http://developer.nvidia.com/cuda-downloads) -- choose Redhat 5.5)

Install git:

    rpm -Uvh http://repo.webtatic.com/yum/centos/5/latest.rpm
    yum install --enablerepo=webtatic --disableexcludes=main git-all

At this point, continue with "installling locally" below.

##Installing Locally

    yum install gcc-c++
    git clone git://github.com/rmtheis/mummergpu
    cd mummergpu
    cd mummergpu-2.0
    cd src
    make
    export LD_LIBRARY_PATH=/usr/local/cuda/lib64
    cd /root/mummergpu/mummergpu-2.0/bin/release
    ./mummergpu
    ./mummergpu ../../data/shortref.fa ../../data/shortqry.fa

##License

[Artistic License](https://github.com/rmtheis/mummergpu/blob/master/mummergpu-2.0/COPYING)
