---
title: "(Optional extra: not part of the course) Cirrus software environment "
teaching: 30
exercises: 10
questions:
- "What does the Cirrus software environment look like and how do I access software?"
- "How can I find out what software is available?"
- "How can I request, install or get help with software on Cirrus?"
objectives:
- "Know how to access different software on Cirrus using software modules."
- "Know how to find out what is installed and where to get help."
keypoints:
- "Software is available through modules."
- "The CSE service can help with software issues."
---

## Using software modules on Cirrus

Cirrus software modules use the
[Lmod](https://lmod.readthedocs.io/) system to provide
access to different software and versions on the system. The modules and versions available will
change across the lifetime of the service.

Software modules are provided by both HPE and the Cirrus CSE team at
[EPCC](https://www.epcc.ed.ac.uk).

## What modules are loaded when you log into Cirrus?

All users start with a default set of modules loaded into their environment. These include:

   - a module to set up the default Cirrus environment
   - a module to load default Cirrus utilities
   - a module to allow standard use of Git functions

You can see what modules you currently have loaded with the `module list` command:

```
auser@cirrus-login2:~> module list
```
{: .language-bash}
```

Currently Loaded Modulefiles:
 1) git/2.37.3
 2) epcc/utils
 3) /mnt/lustre/e1000/home/y07/shared/cirrus-modulefiles/epcc/setup-env

```
{: .output}

> ## Getting back if you purge or make a mistake
> 
> Unlike many other HPC systems you may have used, you should not generally use the
> `module purge` command before starting to use the system. Some of the modules
> loaded by default are required for you to be able to use the system correctly and
> so many things will not work if you use `module purge`. If you need to change the
> setup, you will generally use the `module load` command instead.
>
> If you do find yourself with a broken environment you can usually fix things by
> logging out and logging back in again.
{: .callout}

## Finding out what software is available

You can query which software is provided by modules with the `module avail` command:

```
auser@cirrus-login2:~> module avail
```
{: .language-bash}
```

--------------------------------------------------- /work/y07/shared/cirrus-modulefiles ---------------------------------------------------
anaconda3/2023.09                      intel-19.5/fc                                            openfoam/v2206
ant/1.10.8(default)                    intel-19.5/itac                                          openfoam/v2306
autoconf/2.71                          intel-19.5/mpi                                           openmpi/4.1.6(default)
automake/1.16.5                        intel-19.5/pxse                                          openmpi/4.1.6-cuda-11.6
autotools/default                      intel-19.5/tbb                                           openmpi/4.1.6-cuda-11.6-nvfortran
binutils/2.36(default)                 intel-19.5/vtune                                         openmpi/4.1.6-cuda-11.8
binutils/2.42                          intel-20.4/advisor                                       openmpi/4.1.6-cuda-11.8-nvfortran
bison/3.8.2                            intel-20.4/cc                                            openmpi/4.1.6-cuda-12.4
boost/1.84.0                           intel-20.4/cmkl                                          openmpi/4.1.6-cuda-12.4-nvfortran
castep/22.1.1(default)                 intel-20.4/compilers                                     openmpi/5.0.0
cmake/3.25.2                           intel-20.4/fc                                            openmpi/5.0.0-cuda-11.6
cp2k/2023.2                            intel-20.4/itac                                          openssl/3.2.1
CRYSTAL17/1.0.2_intel18(default)       intel-20.4/mpi                                           orca/5.0.3
eigen/3.4.0                            intel-20.4/psxe                                          perf/1.0.0
epcc-reframe/2.0                       intel-20.4/tbb                                           proj/9.1.1
epcc/setup-env                         intel-20.4/vtune                                         pyfr/1.15.0-gpu
epcc/utils                             intel-license                                            python/3.7.16
expat/2.6.0                            java/jdk-14.0.1                                          python/3.8.16-gpu
fftw/3.3.10-gcc10.2-impi20.4           java/jdk-20.0.1                                          python/3.9.13(default)
fftw/3.3.10-gcc10.2-mpt2.25(default)   lammps-gpu/03Mar2024-gcc10.2-impi20.4-cuda12.4           python/3.9.13-gpu
fftw/3.3.10-gcc10.2-ompi4-cuda11.8     lammps-gpu/15Dec2023-gcc10.2-impi20.4-cuda11.8(default)  python/3.10.8-gpu
fftw/3.3.10-gcc12.3-impi20.4           lammps/15Dec2023-gcc10.2-impi20.4(default)               python/3.11.5-gpu
fftw/3.3.10-intel20.4-impi20.4         libsndfile/1.0.28                                        python/3.12.1
fftw/3.3.10-intel20.4-mpt2.25          libtool/2.4.7                                            python/3.12.1-gpu
flex/2.6.4                             matlab/R2020b(default)                                   pytorch/1.13.1(default)
forge/23.1.1                           matlab/R2021b                                            pytorch/1.13.1-gpu
forge/24.0(default)                    matlab/R2023b                                            pytorch/2.2.0-gpu
gaussian/16.A03(default)               metis/5.1.0                                              quantum-espresso/6.5-intel-20.4
gcc/8.2.0                              mpc/1.1.0                                                quantum-espresso/7.1-gcc10.2-mpt2.25
gcc/10.2.0(default)                    mpfr/4.2.1-gcc(default)                                  quantum-espresso/7.1-intel20.4-impi
gcc/12.3.0-offload                     mpfr/4.2.1-intel                                         R/4.3.2(default)
gdal/3.6.2-gcc                         namd/2.14(default)                                       reframe/4.6.0
gdb/9.2(default)                       namd/2.14-nosmp                                          scalasca/2.6-gcc10-mpt225
git/2.37.3                             namd/3.0a13-gpu                                          singularity/3.7.2
git/2.43.3                             namd/3.0a13-gpu-multi                                    spindle/0.13
gmp/6.3.0-gcc(default)                 namd/2022.07.21-gpu                                      sqlite/3.40.1
gmp/6.3.0-intel                        ncl/6.6.2                                                strace/5.8(default)
gnu-parallel/20240122-gcc10            nco/5.1.9(default)                                       strace/6.7
gnuplot/5.4.0(default)                 ncview/2.1.10                                            svn/1.14.3
graphviz/10.0.1                        netcdf-parallel/4.9.2-intel20-impi20                     tensorflow/2.13.0
gromacs-gpu/2023.4                     netcdf-parallel/4.9.2-intel20-mpt225                     tensorflow/2.13.0-gpu
gromacs-gpu/2023.5(default)            ninja/1.10.2(default)                                    tensorflow/2.15.0(default)
gromacs/2023.4(default)                nvidia/cudnn/8.6.0-cuda-11.6(default)                    tmux/3.3a(default)
gromacs/2023.4-gpu                     nvidia/cudnn/8.6.0-cuda-11.8                             ucx/1.15.0(default)
gromacs/2023.5-gpu                     nvidia/cudnn/9.2.0-cuda-12.4                             ucx/1.15.0-cuda-11.6
gsl/2.7                                nvidia/nvhpc-byo-compiler/22.2                           ucx/1.15.0-cuda-11.8
hdf5parallel/1.10.6-gcc8-mpt225        nvidia/nvhpc-byo-compiler/22.11                          ucx/1.16.0
hdf5parallel/1.12.0-nvhpc-openmpi      nvidia/nvhpc-byo-compiler/24.5                           ucx/1.16.0-cuda-12.4
hdf5parallel/1.14.1-2_cuda116_ompi414  nvidia/nvhpc-nompi/22.2                                  udunits/2.2.26
hdf5parallel/1.14.1-2_cuda118_ompi414  nvidia/nvhpc-nompi/22.11                                 usage-analysis/1.0
hdf5parallel/1.14.3-gcc10-mpt225       nvidia/nvhpc-nompi/24.5                                  valgrind/3.22.0(default)
hdf5parallel/1.14.3-intel20-impi20     nvidia/nvhpc/22.2                                        vasp/5/5.4.4-intel19-mpt220(default)
hdf5parallel/1.14.3-intel20-mpt225     nvidia/nvhpc/22.11                                       vasp/6/6.2.1-intel19-mpt220(default)
hdf5serial/1.14.3-intel20              nvidia/nvhpc/24.5                                        vasp/6/6.3.2-gpu-nvhpc22
htop/3.2.1                             nvidia/tensorrt/8.4.3.1-u2(default)                      vasp/6/6.3.2-intel20-mpt225
ImageMagick/7.0.10-22(default)         nvidia/tensorrt/10.0.1.6                                 vasp/6/6.4.2-gpu-nvhpc22
ImageMagick/7.1.1-28                   nwchem/7.2.0(default)                                    vasp/6/6.4.2-intel20-mpt225
intel-19.5/cc                          oneapi/latest(default)                                   zlib/1.3.1
intel-19.5/cmkl                        openfoam/v10.0
intel-19.5/compilers                   openfoam/v2106

----------------------------------------------------- /usr/share/Modules/modulefiles ------------------------------------------------------
dot  hmpt/2.25  module-git  module-info  modules  mpt/2.25  null  perfboost  use.own
                                         
```
{: .output}

The output lists the available modules and their versions. It also shows you which modules are
loaded by default (marked with `(default)`) when there are multiple versions available and you do
not specify the version when you load.

> ## Licensed software
> Some of the software installed on Cirrus requires the user to have their licence validated before they
> can use it on the service. More information on gaining access to licensed software through the SAFE
> is provided below.
{: .callout}

## Loading and switching modules

Lets look at our environment before we change anything. As you may recall, to
see just our loaded modules we use the `module list` command:

```
auser@cirrus-login2:~> module list
```
{: .language-bash}
```


Currently Loaded Modulefiles:
 1) git/2.37.3
 2) epcc/utils
 3) /mnt/lustre/e1000/home/y07/shared/cirrus-modulefiles/epcc/setup-env

```
{: .output}

You load modules with the `module load` command. For example, to load the `gromacs` module:

```
auser@cirrus-login2:~> module load gromacs
```
{: .language-bash}
```

Loading gromacs/2023.4
  Loading requirement: gcc/10.2.0 intel-license intel-20.4/mpi intel-20.4/cmkl fftw/3.3.10-gcc10.2-impi20.4

```
{: .output}

Now, lets list our loaded modules again with `module list` (you can also use `ml` as
a shortcut for `module list` or `module load` if it has an argument):

```
auser@cirrus-login2:~> ml
```
{: .language-bash}
```

Currently Loaded Modulefiles:
 1) git/2.37.3                                                            4) gcc/10.2.0(default)   7) intel-20.4/cmkl
 2) epcc/utils                                                            5) intel-license         8) fftw/3.3.10-gcc10.2-impi20.4
 3) /mnt/lustre/e1000/home/y07/shared/cirrus-modulefiles/epcc/setup-env   6) intel-20.4/mpi        9) gromacs/2023.4(default)
                   
```
{: .output}

You can see that the default `gromacs` module (`gromacs/2023.4`) has been loaded (loading this
module has also loaded some other modules to match the environment that was used to
compile GROMACS).

## Licensed software

Some of the software installed on Cirrus requires a user to have a valid licence agreed with the 
software owners/developers to be able to use it (for example, VASP). Although you will be able to
load this software on Cirrus you will be barred from actually using it until your licence has been
verified.

You request access to licensed software through the EPCC SAFE (the web administration tool you used
to apply for your account and retrieve your initial password) by being added to the appropriate
*Package Group*. To request access to licensed software:

1. Log in to [SAFE](https://safe.epcc.ed.ac.uk)
2. Go to the Menu *Login accounts* and select the login account which requires access to the software
3. Click *New Package Group Request*
4. Select the software from the list of available packages and click *Select Package Group*
5. Fill in as much information as possible about your license; at the very least provide the information
   requested at the top of the screen such as the licence holder's name and contact details. If you are
   covered by the license because the licence holder is your supervisor, for example, please state this.
6. Click *Submit*

Your request will then be processed by the Cirrus Service Desk who will confirm your license with the
software owners/developers before enabling your access to the software on Cirrus. This can take several
days (depending on how quickly the software owners/developers take to respond) but you will be advised
once this has been done.

## Getting help with software

You can find more information on the software available on Cirrus in the Cirrus Documentation at:

* [Cirrus Documentation](https://docs.cirrus.ac.uk)

This includes information on the software provided by Cray and the software provided by the 
Cirrus CSE Service at EPCC.

If the software you require is not currently available or you are having trouble with the installed
software then please contact
[the Cirrus Service Desk](support@cirrus.ac.uk) and they
will be able to assist you.

{% include links.md %}

