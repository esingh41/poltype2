## Installation


### Required Programs
* Tinker, version >=8.9.4
* GDMA, version 2.3 and greater
* PSI4 (or Gaussian 09/16)
* PySCF, version >=2.7.0, together with `pyscf-dispersion` (installed with the conda environment below; needed unless `dont_use_pyscf` is set — see [PySCF version and the optimization functional](#pyscf-version-and-the-optimization-functional))


### Required Tinker CPU Installation
```
git clone git@github.com:TinkerTools/Tinker.git tinker
cd tinker
cd fftw
./configure --prefix=/path_to_tinkerfolder/fftw/ --enable-openmp --enable-threads
make -j 8
make install
cd ..
cp ./make/Makefile ./source
cd source
```
* Open Makefile and comment out default operating system (MAC, unless you have mac)
* Uncomment the default linux installation lines
* Keep the RENAME line commented out


## Optional Tinker GPU Installation
* **Not required for parameterization purpose.**
* Install tinker CPU first. 
```
git clone git@github.com:TinkerTools/tinker9.git
cd tinker9
cp -r TinkerCPU/* tinker/.
mkdir compile
cd compile
export CUDAHOME=/usr/local/cuda-10.2
export CUDACXX=$CUDAHOME/bin/nvcc
export FC=/usr/bin/gfortran
export CXX=/usr/bin/g++
export ACC=/home/liuchw/shared/nvidia/hpc_sdk/Linux_x86_64/21.1/compilers/bin/nvc++
export opt=release
export host=0
export prec=m
export compute_capability=80
export cuda_dir=$CUDAHOME
export CMAKEHOME=/home/liuchw/shared/cmake3.12/bin/
$CMAKEHOME/cmake ../
make -j
```


### Python Envioronment
* Simply using the following git command to download Poltype
```shell
git clone https://github.com/TinkerTools/poltype2.git master
```

* Install anaconda
* Use the following environment.yml file with `conda env create -f environment.yml` Source: https://github.com/TinkerTools/poltype2/issues/45#issuecomment-2137669523 
* Alternatively, you may use the provided `environment.yml` file (under Environments folder) to create a conda environment named "poltype"
* environment.yml:
```yaml
name: mypoltype2
channels:
  - conda-forge
  - rdkit
  - salilab
dependencies:
  - micromamba
  - psi4
  - geometric
  - mdanalysis
  - openbabel
  - svgutils
  - cairosvg
  - psutil
  - mdtraj
  - openpyxl
  - pdb2pqr
  - pdbfixer
  - rdkit
  - modeller
  - gdma
  - dftd4
  - pip
  - pip:
    - pymbar
    - PyAstronomy
    - ase 
    - jax
    - pyscf>=2.7.0
    - dftd3
    - pyscf-dispersion
```

* Download GDMA-2.3 and greater
* Note do not put source in your default .bashrc profile
* Note do not keep multiple sources for different purposes (unless necessary) in the same .bashrc source file, this can cause internal conflicts to arise

### PySCF version and the optimization functional
* The PySCF backend (used unless `dont_use_pyscf` is set) runs DFT geometry optimizations with the functional given by the `pyscf_opt_meth` keyword in `poltype.ini`; the default is now `wb97x-d3bj`.
* `pyscf-dispersion` is **required** for that default — it supplies the D3BJ correction that PySCF adds on top of the wB97X-V exchange-correlation part. Without it PySCF stops with `RuntimeError: dftd3 not available`. (The standalone `dftd3`/`dftd4` pip packages are not what PySCF calls here; `pyscf-dispersion` ships its own libraries.)
* `wb97x-d3bj` resolves the same way on pyscf 2.7.0, 2.8.0 and 2.14.0 (checked; identical energies), so the version can be moved forward freely.
* The previous default, `wb97x_d3`, only works on **pyscf <= 2.7.0**. There the underscore spelling slipped past PySCF's dispersion blacklist and resolved to libxc `HYB_GGA_XC_WB97X_D3` (XC part only, with no additive D3 term). pyscf 2.8+ raises `NotImplementedError: wb97x_d3 is not supported yet.` for both spellings.
* If you must reproduce results generated with the old default, pin `pyscf==2.7.0` and set `pyscf_opt_meth=wb97x_d3` in `poltype.ini`. `wb97x-d3bj` is a different functional, so energies and optimized geometries will not match pre-2.8 `wb97x_d3` output.

### Install fennol

```
git clone https://github.com/thomasple/FeNNol.git
cd FeNNol
pip install .
```

### Optional explicit Conda Installation
* Only do this if the above .yml file doesnt work
```
conda create --name amoebamdpoltype -c psi4 psi4 --yes
conda install -c conda-forge geometric --yes
conda activate amoebamdpoltype
conda config --add channels conda-forge
conda install mdanalysis --yes
conda install -c conda-forge openbabel --yes
conda install -c rdkit rdkit --yes
pip install PyAstronomy
conda install -c conda-forge svgutils --yes
conda install -c conda-forge cairosvg --yes
conda install -c conda-forge psutil --yes
conda install -c conda-forge mdtraj --yes
conda install -c conda-forge pymbar --yes
conda install -c salilab modeller --yes
conda install -c anaconda openpyxl --yes
conda install -c conda-forge pdb2pqr --yes
conda install -c conda-forge pdbfixer --yes
```

### Required explicit Conda Installation - XTB
```
conda create --name xtbenv
conda activate xtbenv
conda install -c conda-forge xtb --yes
```
* add the path to xtb binary in PATH


### Explicit Conda Installation - ANI
```
conda install -c conda-forge ase --yes
conda install -c conda-forge torchani --yes
conda install -c conda-forge openbabel --yes
```
* Install via ani.yml file (under `Environments`)

### Environment var for running POLTYPE:
 Create environments for Tinker,Gaussian 09,and GDMA. Put these in a file and source it before running POLTYPE so that programs such as "analyze.x" "gdma" "g16" can be found. You can use "which" to verify.
* Example bashrc that you should source before running poltype. Note "mypoltype2" matches the conda env above
```shell
export myusername=`whoami`
conda activate mypoltype2
export g09root=/opt/g09gh/gaussian
source $g09root/g09/bsd/g09.profile
export GAUSS_SCRDIR=/scratch/$myusername/
export GDMADIR=/opt/gdma/gdma-2.3.3/bin/
export PATH=/opt/gdma/gdma-2.3.3/bin/:$PATH
export PSI_SCRATCH=/scratch/$myusername/
# add tinker exe to path
```
