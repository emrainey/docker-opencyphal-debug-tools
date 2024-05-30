# We use the base Ubuntu image on ECR
FROM ubuntu:22.04

ENV DEBIAN_FRONTEND noninteractive

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
RUN python3 -m pip install -Iv pyserial yakut
RUN python3 -m pip check
RUN rm -rf /root/.cache/pip/*

# Create the working directory for processing cyphal DSDL and Yakut
WORKDIR /app

ENV CYPHAL_PATH /app/cyphal/dsdl
ENV YAKUT_PATH ${CYPHAL_PATH}/../generated
RUN mkdir -p ${YAKUT_PATH} ${DSDL_PATH}
COPY public_regulated_data_types/uavcan ${CYPHAL_PATH}/uavcan
RUN yakut -v compile -O ${YAKUT_PATH} ${CYPHAL_PATH}/uavcan
ENV DSDL_PATH ${CYPHAL_PATH}/uavcan
ENV PYTHONPATH ${YAKUT_PATH}

COPY wireshark_plugins/*.lua /root/.local/lib/wireshark/plugins/
RUN setcap 'CAP_NET_RAW+eip CAP_NET_ADMIN+eip' /usr/bin/dumpcap

# Show in the log what this is now
RUN lsb_release -a

# Cyphal UDP Port
EXPOSE 9382/UDP

# Check that it exists and show the version in the log
RUN yakut --version
