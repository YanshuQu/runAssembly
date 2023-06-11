# runAssembly
Packaging Newbler with a singularity container enables _de novo_ assembly in any Linux environment.

## Requirement
### Install system dependencies

You must first install development tools and libraries to your host.

On Debian-based systems, including Ubuntu:

```sh
# Ensure repositories are up-to-date
sudo apt-get update
# Install debian packages for dependencies
sudo apt-get install -y \
    build-essential \
    libseccomp-dev \
    pkg-config \
    squashfs-tools \
    cryptsetup \
    curl wget git
```

On CentOS/RHEL:

```sh
# Install basic tools for compiling
sudo yum groupinstall -y 'Development Tools'
# Ensure EPEL repository is available
sudo yum install -y epel-release
# Install RPM packages for dependencies
sudo yum install -y \
    libseccomp-devel \
    squashfs-tools \
    cryptsetup \
    wget git
```

### Download & install Go Language
[Singularity](https://github.com/apptainer/singularity) is written in [Go language](https://go.dev/), and may require a newer version of Go than is
available in the repositories of your distribution. We recommend installing the
latest version of Go from the [official binaries](https://go.dev/dl/).

```sh
wget https://go.dev/dl/go1.14.12.linux-amd64.tar.gz
tar -C /pub/software -xzvf go1.14.12.linux-amd64.tar.gz
rm go1.14.12.linux-amd64.tar.gz
echo 'export PATH=/pub/software/go/bin:$PATH' >> /etc/profile.d/env.sh
source /etc/profile.d/env.sh
```
### Download & install Singularity
You can configure, build, and install Singularity using the following commands:

```sh
wget https://github.com/hpcng/singularity/releases/download/v3.7.2/singularity-3.7.2.tar.gz
tar -xzf singularity-3.7.2.tar.gz
cd singularity
./mconfig
cd builddir
make
sudo make install
```

And that's it! Now you can check your Singularity version by running:
```sh
singularity --version
```

If you wish to use singularity in any path, we recommend adding singularity to the PATH environment variable.



## _De novo_ assembly with runAssembly.sif
```sh
singularity exec runAssembly.sif runAssembly
```
If the following prompt appears, the installation is successful.
>Error in runAssembly:  No read data files are specified.  
>Usage:  runAssembly [-o projdir] [-nrm] [-p (sfffile | [regionlist:]analysisDir)]... (sfffile | [regionlist:]analysisDir)...
```sh
singularity exec runAssembly.sif runAssembly -cpu 10 -het -sio -m -urt -large -s 100 -nobig -o $output_dir Hifi.cut20k.fa
# -het: Flag to enable Heterozygotic mode, which causes the assembler to choose a path between 2 contigs when there is ambiguity regarding the path that should be taken.
#       By default, the assembler will not choose a path when ambiguity exists
# -sio: Flag to invoke serial I/O. Use for projects with > 4 million reads.
# -m: Keeps sequence data in memory Increases speed
# -urt: Extend contigs using the ends (tips) of single reads
# -large: Speeds up assembly but reduces accuracy
# -nobig: Skip output of large files (.ace, 454AlignmentInfo.tsv) Default: no
# -o <output-directory>
```
