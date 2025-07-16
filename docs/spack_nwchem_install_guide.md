Here is a short list of commands to get NWChem (hopefully) up and running on the HiPerGator cluster.
The gist is that we are going to be using a package manager called Spack, which we will clone from
GitHub to be able to use.

Spack essentially is a "cookbook" of different ways to build different
packages. It builds everything from source, except for the compilers, which you need to supply
locally. We'll add the NWChem package to a Spack-managed environment, and afterwards all that is
needed is to source the Spack environment setup script and activate the Spack environment. Then we
can build the NWChem package.

This will most likely take a while (around 2 hours), here are the instructions:

```bash
# While on HiPerGator

# Clones Spack repo
git clone --depth=2 https://github.com/spack/spack.git

# Checks out the correct branch of Spack
cd spack
git checkout v0.23.1

# Sets environment variables for Spack (Similar to a python environment, but not exactly)
# assuming you're in the spack/ folder
source ./share/spack/setup-env.sh

# Create the Spack environment (can be named anything, I chose nwchem)
spack env create nwchem
spack env activate nwchem 

# Load compiler modules
module load gcc

# Help Spack find compilers
spack compiler find

# Install NWChem
spack install -j 8 --add nwchem armci=mpi-pr +openmp %gcc ^amdblis threads=openmp ^amdlibflame ^amdscalapack ^openmpi fabrics=cma,ucx
```

The flags for the spack install command are the following:


- `-j 8` : Specifies only using 8 cores (since this is on a login node)
- `--add` : adds the package to the activated spack environment
- `nwchem`  : specifies nwchem is to be installed
- `armci=mpi-pr` : armci version to use for nwchem
- `+openmp` : include the optional openmp setting for nwchem
- `%gcc` : use the gcc compiler, aocc is not on HiPerGator
- `^amdblis threads=openmp` : overrides the original nwchem specs with amdblis as a dependency with the threads option set to openmp
- `^amdlibflame` : overrides the original nwchem specs with amdlibflame as a dependency
- `^amdscalapack` : overrrides the original nwchem specs with amdscalapack as a dependency
- `^openmpi fabrics=cma,ucx` : overrides the original nwchem specs with openmpi as a dependency, with fabrics settings set to cma, ucx.

Info obtained mostly from the AMD [website](https://www.amd.com/en/developer/zen-software-studio/applications/spack/nwchem.html)
regarding installation options for Spack.
