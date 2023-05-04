# Container with 10x spaceranger

#To build this file:
#sudo docker build . -t sydneyinformaticshub/matlab:r2021a

#To run this, mounting your current host directory in the container directory,
# at /project, and excute an example run:
#sudo docker run -it -v `pwd`:/project sydneyinformaticshub/matlab:r2021a /bin/bash -c "spaceranger testrun --id=tiny"

#To push to docker hub:
#sudo docker push sydneyinformaticshub/matlab:r2021a

#To build a singularity container
#export SINGULARITY_CACHEDIR=`pwd`
#export SINGULARITY_TMPDIR=`pwd`
#singularity build spaceranger.img docker://sydneyinformaticshub/matlab:r2021a

#To run the singularity image (noting singularity mounts the current folder by default)
#singularity run --bind /project:/project spaceranger.img /bin/bash -c "cd "$PBS_O_WORKDIR" && export  matlab testrun --id=tiny"

# Pull base image.
# FROM centos:centos7
FROM nvidia/cuda:10.2-cudnn8-devel-centos7
MAINTAINER Nathaniel Butterworth USYD SIH

ENV TZ="Etc/UTC"

RUN mkdir /project /scratch && touch /usr/bin/nvidia-smi

# Set up centos dependencies
RUN yum -y update && \
 yum -y groupinstall 'Development Tools' && \
 yum -y install ca-certificates alsa-lib atk glibc cairo-gobject cairo gstreamer1-plugins-base gstreamer1 gtk3 nspr nss unixODBC pam pango python27 python38 libselinux libSM libsndfile libuuid libX11 libXcomposite libXcursor libXdamage libXext libXfixes libXft libXi libXinerama libXrandr libXrender libXt libXtst libXxf86vm glibc-common glibc-all-langpacks make net-tools procps-ng sudo tzdata unzip wget zlib && \
 yum clean all && \
 rm -rf /var/cache/yum

 # To specify which MATLAB release to install in the container, edit the value of the MATLAB_RELEASE argument.
 # Use lower case to specify the release, for example: ARG MATLAB_RELEASE=r2021b
 ARG MATLAB_RELEASE=r2021a

 # Declare the global argument to use at the current build stage
 ARG MATLAB_RELEASE

 # Run mpm to install MATLAB in the target location and delete the mpm installation afterwards.
 # If mpm fails to install successfully then output the logfile to the terminal, otherwise cleanup.
 RUN wget -q https://www.mathworks.com/mpm/glnxa64/mpm \
     && chmod +x mpm \
     && ./mpm install \
     --release=${MATLAB_RELEASE} \
     --destination=/opt/matlab \
     --products MATLAB   Parallel_Computing_Toolbox\
     || (echo "MPM Installation Failure. See below for more information:" && cat /tmp/mathworks_root.log && false) \
     && rm -f mpm /tmp/mathworks_root.log \
     && ln -s /opt/matlab/bin/matlab /usr/local/bin/matlab

 # Add "matlab" user and grant sudo permission.
 # RUN adduser --shell /bin/bash --gecos "" matlab \
 #     && echo "matlab ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/matlab \
 #     && chmod 0440 /etc/sudoers.d/matlab

 # One of the following 2 ways of configuring the license server to use must be
 # uncommented.

 #ARG LICENSE_SERVER
 # Specify the host and port of the machine that serves the network licenses
 # if you want to bind in the license info as an environment variable. This
 # is the preferred option for licensing. It is either possible to build with
 # something like --build-arg LICENSE_SERVER=27000@MyServerName, alternatively
 # you could specify the license server directly using
 #       ENV MLM_LICENSE_FILE=27000@flexlm-server-name
 #ENV MLM_LICENSE_FILE=$LICENSE_SERVER

 # Alternatively you can put a license file into the container.
 # You should fill this file out with the details of the license
 # server you want to use and uncomment the following line.
 COPY network.lic /opt/matlab/licenses/

 # The following environment variables allow MathWorks to understand how this MathWorks
 # product (MATLAB Dockerfile) is being used. This information helps us make MATLAB even better.
 # Your content, and information about the content within your files, is not shared with MathWorks.
 # To opt out of this service, delete the environment variables defined in the following line.
 # See the Help Make MATLAB Even Better section in the accompanying README to learn more:
 # https://github.com/mathworks-ref-arch/matlab-dockerfile#help-make-matlab-even-better
 # ENV MW_DDUX_FORCE_ENABLE=true MW_CONTEXT_TAGS=MATLAB:DOCKERFILE:V1

 # Set user and work directory
 # USER matlab
 # WORKDIR /home/matlab
 CMD ["matlab"]
 # CMD [""]

 # RUN [ -d /usr/share/X11/xkb ] || mkdir -p /usr/share/X11/xkb
