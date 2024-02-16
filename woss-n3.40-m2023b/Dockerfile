# syntax=docker/dockerfile:1

# MIT License

# Copyright (c) 2024 Emanuele Giona <giona.emanuele@gmail.com> (SENSES Lab, 
# Sapienza University of Rome)

# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the "Software"), to deal
# in the Software without restriction, including without limitation the rights
# to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
# copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:

# The above copyright notice and this permission notice shall be included in all
# copies or substantial portions of the Software.

# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
# SOFTWARE.

FROM egiona/ns3-woss:u22.04-n3.40-w1.12.6

LABEL maintainer="giona.emanuele@gmail.com"
LABEL version="0.1"
LABEL description="ns3-woss:u22.04-n3.40-w1.12.6 and MATLAB Runtime 2023b (23.2)"

# Setting MCR_UPDATE to 0 yields the installation of the selected MATLAB Runtime version as originally released (no other updates)
ARG MCR_VER="2023b"
ARG MCR_UPDATE="6"

# === MATLAB Runtime installer URL & filename ===
# No updates
ARG MCR_FNAME_RELEASE="MATLAB_Runtime_R${MCR_VER}_glnxa64.zip"
# Updates
ARG MCR_FNAME_UPDATES="MATLAB_Runtime_R${MCR_VER}_Update_${MCR_UPDATE}_glnxa64.zip"

ARG MCR_URL_BASE="https://ssd.mathworks.com/supportfiles/downloads/R${MCR_VER}/Release/${MCR_UPDATE}/deployment_files/installer/complete/glnxa64"

# Installation directory for MATLAB Runtime; also available as environment variable in containers
ENV MCR_DIR="/usr/local/MATLAB/MATLAB_Runtime"
ARG MCR_LIBS="$MCR_DIR/R${MCR_VER}/runtime/glnxa64:$MCR_DIR/R${MCR_VER}/bin/glnxa64:$MCR_DIR/R${MCR_VER}/sys/os/glnxa64:$MCR_DIR/R${MCR_VER}/extern/bin/glnxa64"
# === ===== ===== ===

ARG TMP_DOWNLOAD_DIR="/home/downloads"
# ===== Basic OS updates =====
ARG DEBIAN_FRONTEND=noninteractive
RUN apt -y update && apt -y upgrade && \
# ===== Downloading & installing: MATLAB Runtime =====
 cd /home && mkdir "$TMP_DOWNLOAD_DIR" && cd "$TMP_DOWNLOAD_DIR" && \
 # Use the base filename or the update-specific one (exactly 1 of the following conditionals is executed)
 if [ "${MCR_UPDATE}" -eq "0" ]; then wget "${MCR_URL_BASE}/${MCR_FNAME_RELEASE}"; unzip "${MCR_FNAME_RELEASE}"; fi && \
 if [ "${MCR_UPDATE}" -ne "0" ]; then wget "${MCR_URL_BASE}/${MCR_FNAME_UPDATES}"; unzip "${MCR_FNAME_UPDATES}"; fi && \
 # Installing MATLAB Runtime (non-interactively & specifying directory)
 echo "agreeToLicense=yes" >> install_options.txt && \
 echo "destinationFolder=$MCR_DIR" >> install_options.txt && \
 ./install -inputfile install_options.txt && \
 # Add to MATLAB Runtime libraries to LD_LIBRARY_PATH (exactly 1 of the following conditionals is executed)
 echo "" >> ~/.bashrc && \
 echo "# MATLAB Runtime libraries for executables' dependencies" >> ~/.bashrc && \
 if [ -z "${LD_LIBRARY_PATH}" ]; then echo "export LD_LIBRARY_PATH=$MCR_LIBS" >> ~/.bashrc; fi && \
 if [ -n "${LD_LIBRARY_PATH}" ]; then echo "export LD_LIBRARY_PATH=$MCR_LIBS:$LD_LIBRARY_PATH" >> ~/.bashrc; fi && \
 # Cleanup: temporary downloads directory
 cd /home && rm -rf "$TMP_DOWNLOAD_DIR"

# Fix the directory for the specific version installed
ENV MCR_DIR="/usr/local/MATLAB/MATLAB_Runtime/R${MCR_VER}"
CMD ["/bin/bash"]
