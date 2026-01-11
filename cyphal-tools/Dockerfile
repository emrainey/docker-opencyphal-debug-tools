# We use the base Ubuntu image on ECR
FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive

# Change the default shell to /bin/bash
RUN chsh -s /bin/bash root

RUN echo 'APT::Install-Suggests "0";' >> /etc/apt/apt.conf.d/00-docker
RUN echo 'APT::Install-Recommends "0";' >> /etc/apt/apt.conf.d/00-docker

# Update to latest! (Show on command line so we can debug later) and get rid of apts
RUN apt update
RUN apt list --upgradable
RUN apt upgrade -y
RUN apt install -fy
RUN apt install -y debconf
RUN echo "resolvconf resolvconf/linkify-resolvconf boolean false" | debconf-set-selections
RUN apt install -y --reinstall resolvconf
RUN apt install -y ubuntu-minimal dbus usbutils can-utils tcpdump tshark screen tmux joe bash-completion autoconf libtool \
    python3.11 python3-pip python3-dev
RUN apt clean && rm -r /var/lib/apt/lists/*

# Pips to install
RUN python3 -m pip install --upgrade pip
RUN python3 -m pip install -Iv pyserial yakut==0.13.0
RUN python3 -m pip check
RUN rm -rf /root/.cache/pip/*

# Create the working directory for processing cyphal DSDL and Yakut
WORKDIR /app
# The root of all the cyphal files
ENV CYPHAL_ROOT=/app/cyphal
# Directory where DSDL files are located
ENV DSDL_PATH=${CYPHAL_ROOT}/dsdl
# Directory where Yakut will generate Python modules from DSDL
ENV YAKUT_PATH=${CYPHAL_ROOT}/generated
# When running Yakut, it needs to find the generated Python modules
ENV PYTHONPATH=${YAKUT_PATH}
# Where Cyphal can find the dsdl to compile automatically
ENV CYPHAL_PATH=${DSDL_PATH}/uavcan
# Used by Nunavut as well
ENV DSDL_INCLUDE_PATH=${DSDL_PATH}/uavcan
RUN mkdir -p ${CYPHAL_ROOT} ${DSDL_PATH} ${YAKUT_PATH} ${DSDL_INCLUDE_PATH}
COPY public_regulated_data_types/uavcan ${DSDL_PATH}/uavcan
RUN yakut -v compile -O ${YAKUT_PATH} ${DSDL_PATH}/uavcan

COPY wireshark_plugins/*.lua /root/.local/lib/wireshark/plugins/
RUN setcap 'CAP_NET_RAW+eip CAP_NET_ADMIN+eip' /usr/bin/dumpcap

# Show in the log what this is now
RUN lsb_release -a

# Cyphal UDP Port
EXPOSE 9382/udp

# Check that it exists and show the version in the log
ENV UAVCAN__NODE__ID=126
ENV UAVCAN__UDP__IFACE=127.0.0.1
RUN yakut --version
