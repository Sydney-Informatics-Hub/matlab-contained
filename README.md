# Matlab Container

Docker/Singularity image to run [Matlab](https://github.com/mathworks-ref-arch/getting-started-with-matlab-in-docker) on Centos 6.9 kernel.

Mathworks provides Matlab as [Docker containers](https://hub.docker.com/r/mathworks/matlab) but unfortunatley, they all have later base operating system versions so will not work (even in a container) on Artemis. This repo has modified the Dockerfiles provided by Mathworks to run on our old Centos 6.9 cluster. It uses a University license file, so it can only run on the Sydney ICT network (on campus or via VPN).

The install script currently only has limited toolboxes, but can be modified (see the *installer_input.txt*).

Rember to pass `--gpus all` if needing gpu!


If you have used this work for a publication, you must acknowledge SIH, e.g: "The authors acknowledge the technical assistance provided by the Sydney Informatics Hub, a Core Research Facility of the University of Sydney."


# Quickstart for Artemis

Put this repo on Artemis e.g.

```
cd /project/<YOUR_PROJECT>
git clone https://github.com/Sydney-Informatics-Hub/matlab-contained.git
```
Then `cd matlab-contained` and modify the `run_artemis.pbs` script and launch with `qsub run_artemis.pbs`.

Otherwise here are the full instructions for getting there....


# How to recreate

## Build with docker
Check out this repo then build the Docker file.
```
sudo docker build . -t sydneyinformaticshub/matlab:r2021a
```

## Run with docker.
To run this, mounting your current host directory in the container directory, at /project, and execute a run on the test images (that live in the container) run:
```
sudo docker run -it -v `pwd`:/project sydneyinformaticshub/matlab:r2021a /bin/bash -c "matlab test.m"
```

## Push to docker hub
```
sudo docker push sydneyinformaticshub/matlab:r2021a
```

See the repo at [https://hub.docker.com/r/sydneyinformaticshub/matlab](https://hub.docker.com/r/sydneyinformaticshub/matlab)


## Build with singularity
```
export SINGULARITY_CACHEDIR=`pwd`
export SINGULARITY_TMPDIR=`pwd`

singularity build matlab.img docker://sydneyinformaticshub/matlab:r2021a
```

## Run with singularity
To run the singularity image (noting singularity mounts the current folder by default)
```
singularity run --bind /project:/project matlab.img /bin/bash -c "cd "$PBS_O_WORKDIR" && matlab test.m"
```
