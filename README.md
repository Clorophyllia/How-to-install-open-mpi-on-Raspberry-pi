# How to install open mpi on Raspberry pi (fixing the arm)
 MPI is most famous communication protocol for parallel computing. As time passed, a lot of parallel programming languages have been sprang up, but still, MPI is dominant in high performance computing. The key characteristic of MPI is versatility. Thus, its uses isn’t confined to supercomputer. Actually, MPI is fascinating tool to construct distributed system which consist of simple and identical machines. There are 2 major implementation in MPI. First one is MPICH, and second one is Open MPI.

# known Issue
 Since last year, as I’ve been working on some project, I found that there were some issues in terms of configuring Open MPI on RPi. When I trying to build mpi, I have to show the following message all the time :

    Error: selected processor does not support ARM mode `dmb'

 At the time, I just bypassed by using mpich instead of OMPI or install the old version from Raspbian Software Center ( equivalent of basic application : Add/ Remove Software, this method is not work recently though .. ), because I couldn’t find firm	answer from google.
 Recently, I succeeded in solve the problem, which I struggled with. I hope the people who want to use MPI on Raspberry pi (but suffer from same problem) get some help from this simple guide.

# solution

### Operation environment

    Raspberry pi 3
    Linux raspberrypi 4.4.34-v7+ armv7l GNU/Linux
    gcc(Raspbian 4.9.2-10) 4.9.2

 You might aware that the architecture showed up is different with real hardware architecture (cortex-a53, armv8) it because RPi is working on 32-bit OS. Similarly, this sort of thing is happened in case of RPi1 and RPi2 (as armv6l).  It’s the principal cause of make command failure. Because of this, while processing configure script raspberry pi capture its own information in a wrong way. So, we should give integral instruction manually using gcc flag option.

## Step 1 : download and extract open mpi 
 At first, you need to download lastest stable version of open mpi from official (https://www.open-mpi.org/software/ompi/v3.0/), or you can just type following line :

    wget https://www.open-mpi.org/software/ompi/v3.0/downloads/openmpi-3.0.1.tar.gz

Likewise, in odrder to extract the downloaded file and change directory :

    tar xvzf openmpi-3.0.1.tar.gz
    cd openmpi-3.0.1


## Step 2 : give ARM option when configure
    ./configure CFLAGS=”-mcpu=cortex-a53 -mfloat-abi=hard -mfpu=neon-fp-armv8 -mneon-for-64bits”
 
    make

    make install

 Of course, detail variable is depend on your setup. For example, if you want to build OMPI on RPi2, than you need to put CFLAGS=”-mcpu=cortex-7 -mfloat-abi=hard -mfpu=neon-vfpv4” instead of flag for RPi3 in upper box.

## Step 3 (optional) : use ldconfig
 In the process of installation, I found several library linking errors were occurred. It can cause another problem when compile mpi program.

    mpicc: error while loading shared libraries: libopen-pal.so.4: cannot open shared object file: No such file or directory
 
But ldconfig will create links for mpi easily. Only thing we have to do is type following command on terminal.

    sudo ldconfig

After all, with these simple steps, now we can compile and run the mpi program. 

## References
- [GCC ARM OPTIONS]( https://gcc.gnu.org/onlinedocs/gcc/ARM-Options.html)
- [GCC compiler optimization for ARM-based systems](https://gist.github.com/fm4dd/c663217935dc17f0fc73c9c81b0aa845)
- [LDCONFIG Linux Programmer’s Manual]( http://man7.org/linux/man-pages/man8/ldconfig.8.html)
- [Building Open MPI](https://www.open-mpi.org/faq/?category=building)
