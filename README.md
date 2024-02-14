# ns-3 & MATLAB Docker images

[ns-3][ns3] and [MATLAB Runtime][matlab-runtime] bundles using [Docker][docker].

The scope of this repository is to automate the installation process of both ns-3 and MATLAB Runtime, in order to provide a hassle-free setup process for a simulation environment.

Images are based on either [`egiona/ns3-base`][ns3-base-docker] or [`egiona/ns3-woss`][ns3-woss-docker], shipping with MATLAB Runtime. 
Please refer to [`ns3-base`][ns3-base] or [`ns3-woss`][ns3-woss] repositories for further details regarding available ns-3 and/or WOSS configurations and utilities.

## Available configurations *(latest first)*

Docker image name: [**`egiona/ns3-matlab`**][docker-hub-matlab].

| Docker image tag | Base image <br> `egiona/ns3-base` | MATLAB Runtime | Dockerfile |
| :---: | :---: | :---: | :---: |
| [`base-n3.40-m2022b`][image-base1] | [`u22.04-n3.40`][base1] | [2022b][matlab-changelog] | [link][file-base1] |

| Docker image tag | Base image <br> `egiona/ns3-woss` | MATLAB Runtime | Dockerfile |
| :---: | :---: | :---: | :---: |
| [`woss-n3.40-m2022b`][image-woss1] | [`u22.04-n3.40-w1.12.6`][woss1] | [2022b][matlab-changelog] | [link][file-woss1] |

Full changelog can be found at [this page](./CHANGELOG.md).

<!-- ### Discontinued images

The following image tags have been discontinued and are not available from the DockerHub repository.

If you are using any of these tags, please consider switching to a different one that is still supported.

| Docker image tag | Reason | Date |
| :---: | :---: | :---: |
| `u20.04-n3.37-w1.12.5` <br> `u18.04-n3.37-w1.12.4-r2` <br> `u18.04-n3.37-w1.12.4` | GCC compiler issues; <br> not solved by Ubuntu 20.04 upgrade | 2023/10/11 | -->

### Contributing

Any problems should be reported via the GitHub issue tracker.

Users are welcomed to contribute new images (_e.g._ different base image or other ns-3 versions) via Pull Request and adhering to the following style:

- Directory named `<A-B-C>` with: `A` equal to an arbitrary versioned base image short-hand (_i.e._ `base` refers to `ns3-base`, whereas `woss` refers to `ns3-woss` used as starting image); `B` equal to the ns-3 version bundled (_i.e._ `n3.40` refers to ns-3.40); and `C` equal to the MATLAB Runtime version bundled (_i.e._ `m2022b` refers to MATLAB Runtime for MATLAB 2022b).

    Such directory name will also be used as image tag.

- The directory shall contain a well-commented `Dockerfile` for the image creation.

    Other contents may be freely modified.

# Usage guidelines

## Core instructions

> The following instructions should apply to all platforms supported by Docker. 
However, _utility scripts_ are only provided for UNIX-like systems.

1. Install Docker (please refer to [official guidelines][docker-install] w.r.t. your own OS)

2. Select your desired Docker image according to the table above using

    `docker pull egiona/ns3-matlab:<tag>`

3. Retrieve the desired image identifier using 

    `docker images`

4. Launch a container using the selected image using 

    `docker run -td --name <container name> <image ID>`

5. Launch a live terminal from the container using 

    `docker exec -it <container ID or name> /bin/bash`

    _You can obtain a running container's ID using_&nbsp; `docker ps` _, or_&nbsp; `docker container ls -a` _(the latter also includes containers in any state)._

6. Be aware that, prior to its usage with hereby Docker images, all MATLAB code must be compiled under a licensed MATLAB environment comprehensive of [MATLAB Compiler][matlab-compiler].

    Please ensure that the desired Docker image is bundled with a MATLAB Runtime version **greater or equal** than that of your MATLAB installation.

    For instance: `ns3-matlab:base-n3.40-m2022b` supports executables created by MATLAB w/ Compiler versions _up to_ 2022b (included).

    Please refer to [MATLAB Compiler instructions][matlab-mcc-linux] for compiling your MATLAB source code into Linux-compatible executables.

## Utility scripts

1. You can switch between `debug` and `optimized` builds of ns-3 (see [details][ns3-builds]) using 

    [`./build-debug.sh`][latest-debug] or [`./build-optimized.sh`][latest-optimized] respectively 

    The aforementioned utility scripts are placed in the directory `/home` of a container's filesystem for both base images `ns3-base` and `ns3-woss`.

2. A utility script in the form of a [Makefile][latest-makefile] is provided.

    Similarly to [build scripts][latest-build], this utility Makefile is placed in the directory `/home` of a container's filesystem.

    This script allows for easy decoupling of development directory from ns-3's source directory.
    Indeed, it is possible to keep novel modules and program driver scripts outside `src` (or `contrib`) and `scratch` directories of the ns-3 installation directory during development, and only copying them afterwards.
    This is especially useful when paired with mounted directories.

    Multiple targets are present, allowing: ns-3 current version checking, compilation and execution of simulation driver programs (copying them to `scratch` subdir first), management of ns-3 modules (creation in `contrib` subdir and copy outside, synchronization of contents, elimination), and debugging (GNU debugger, Valgrind, ns-3 tests).

    Use the following command for all details:

    `make help`

## Optional instructions

> As long as you `docker restart` the same container, any modification to its contents will be preserved.
However, it is advisable to keep a _local backup copy_ of your modules and experiment results.

1. Copy an arbitrary local file into the container's filesystem using

    `docker cp <path/to/file> <container ID>:<desired/path/to/file>`

2. Copy an arbitrary container's file to local filesystem using

    `docker cp <container ID>:<path/to/file> <local/path/to/file>`

3. Mount a local directory into a container (just once, instead of `docker run`) using

    `docker run -td --mount type=bind,source=<local/FS/path>,target=<container/FS/path> --name <container name> <image ID>`

    _Paths to be mounted must be absolute._

    _This is only needed the **first time** a container is instantiated, subsequent calls to_&nbsp; `docker start` _on the same container will automatically load the mounted directory._

4. An environment variable `CXX_CONFIG` is available for user-defined scripts to adapt their GCC compilation parameters; by default, such variable holds the following contents:

    `CXX_CONFIG="-Wall -Werror -Wno-unused-variable"`

    Moreover, [build scripts][latest-build] have been updated to provide an exit value reflective of ns-3's configuration and build outcome.

# Citing this work

If you use any of the Docker images described in this repository, please cite this work using any of the following methods:

**APA**
```
Giona, E. ns-3 and MATLAB Runtime Docker images [Computer software]. https://doi.org/TODO
```

**BibTeX**
```
@software{Giona_ns-3_and_MATLAB,
author = {Giona, Emanuele},
doi = {TODO},
license = {MIT},
title = {{ns-3 and MATLAB Runtime Docker images}},
url = {https://github.com/emanuelegiona/ns3-matlab-docker}
}
```

Bibliography entries generated using [Citation File Format][cff] described in the [CITATION.cff][citation] file.

# License

**Copyright (c) 2024 Emanuele Giona ([SENSES Lab][senseslab], Sapienza University of Rome)**

This repository and Docker images themselves are distributed under [MIT license][docker-license].

However, ns-3, WOSS, and MATLAB Runtime are distributed under their respective licenses:
[ns-3 license][ns3-license], [WOSS license][woss-license], MATLAB Runtime license ([2022b][mcr-license2022b]).
All installed packages may also be subject to their own license, and the license chosen for the Docker images does not necessarily apply to them.

**Diclaimer: Docker, Ubuntu, ns-3, WOSS, MATLAB, MATLAB Runtime and other cited or included software belongs to their respective owners.**
**Moreover, this shall NOT be considered an official MathWorks product.**



[ns3]: https://www.nsnam.org/
[matlab-runtime]: https://www.mathworks.com/products/compiler/matlab-runtime.html
[docker]: https://www.docker.com/

[ns3-base-docker]: https://hub.docker.com/r/egiona/ns3-base
[ns3-woss-docker]: https://hub.docker.com/r/egiona/ns3-woss
[ns3-base]: https://github.com/emanuelegiona/ns3-base-docker
[ns3-woss]: https://github.com/emanuelegiona/ns3-woss-docker

[docker-hub-matlab]: https://hub.docker.com/r/egiona/ns3-matlab

[matlab-changelog]: https://www.mathworks.com/help/matlab/release-notes.html

[latest-debug]: https://github.com/emanuelegiona/ns3-base-docker/u22.04-n3.40/ns3-build/build-debug.sh
[latest-optimized]: https://github.com/emanuelegiona/ns3-base-docker/u22.04-n3.40/ns3-build/build-optimized.sh
[latest-build]: https://github.com/emanuelegiona/ns3-base-docker/u22.04-n3.40/ns3-build/
[latest-makefile]: https://github.com/emanuelegiona/ns3-base-docker/u22.04-n3.40/ns3-utils/Makefile

<!-- Base: ns3-base -->
[image-base1]: https://hub.docker.com/r/egiona/ns3-matlab/tags?page=1&name=base-n3.40-m2022b
[base1]: https://hub.docker.com/r/egiona/ns3-base/tags?page=1&name=u22.04-n3.40
[file-base1]: ./base-n3.40-m2022b/Dockerfile

<!-- Base: ns3-woss -->
[image-woss1]: https://hub.docker.com/r/egiona/ns3-matlab/tags?page=1&name=woss-n3.40-m2022b
[woss1]: https://hub.docker.com/r/egiona/ns3-woss/tags?page=1&name=u22.04-n3.40-w1.12.6
[file-woss1]: ./woss-n3.40-m2022b/Dockerfile

[docker-install]: https://docs.docker.com/engine/install/
[matlab-compiler]: https://www.mathworks.com/products/compiler.html
[matlab-mcc-linux]: https://www.mathworks.com/help/compiler/

[ns3-builds]: https://www.nsnam.org/docs/release/3.40/tutorial/html/getting-started.html#build-profiles

[cff]: https://citation-file-format.github.io/
[citation]: ./CITATION.cff

[senseslab]: https://senseslab.diag.uniroma1.it/
[docker-license]: ./LICENSE
[ns3-license]: https://www.nsnam.org/develop/contributing-code/licensing/
[woss-license]: https://woss.dei.unipd.it/woss/doxygen/License.html
[mcr-license2022b]: ./mcr-licenses/mcr-2022b.pdf
