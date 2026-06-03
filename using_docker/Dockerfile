##########################################################
# Dockerfile for configuring the IHP 130 nm CAD tools     #
##########################################################
# The main idea of this Dockerfile is to set up an Ubuntu (24.04)
# environment that can be accessed via SSH on port <CONTAINER_PORT_ON_HOST> (host) → 22 (docker).

# Using ubuntu 24.04
FROM ubuntu:24.04

# User configurations
ARG USER_NAME=student01
ARG USER_PASS=admin123

# Tool versions
ARG KLAYOUT_VERSION=0.30.9-1
ARG OPENVAF_VERSION=23_5_0
ARG NGSPICE_BRANCH=ngspice-46

# Update and dependency installation
RUN apt update
RUN apt install -y sudo

# Admin users
RUN useradd -m -s /bin/bash $USER_NAME
RUN echo "$USER_NAME ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
RUN echo "$USER_NAME:$USER_PASS" | chpasswd
RUN echo "root:$USER_PASS" | chpasswd

USER ${USER_NAME}
#suggested by https://ihp-open-pdk-docs.readthedocs.io/en/latest/install/dependencies.html
RUN sudo apt install -y build-essential
RUN sudo apt install -y qtbase5-dev qttools5-dev
RUN sudo apt install -y clang cmake libtool autoconf
RUN sudo apt install -y python3 python3-dev python3-pip python3-virtualenv python3-venv
RUN sudo apt install -y ruby ruby-dev
RUN sudo apt install -y btop tree xterm graphviz git
RUN sudo apt install -y octave octave-dev
RUN sudo apt install -y libqt5xmlpatterns5-dev qtmultimedia5-dev libqt5multimediawidgets5 libqt5svg5-dev libqt5opengl5
RUN sudo apt install -y tcl8.6 tcl-dev tcl8.6-dev
RUN sudo apt install -y tk8.6 tk8.6-dev
RUN sudo apt install -y flex clang gawk xdot pkg-config bison curl help2man perl time
RUN sudo apt install -y libxpm4 libxpm-dev libgtk-3-dev libffi-dev
RUN sudo apt install -y libjpeg-dev libfl-dev libfl2
RUN sudo apt install -y libreadline-dev gettext
RUN sudo apt install -y libboost-system-dev libboost-python-dev libboost-filesystem-dev zlib1g-dev
RUN sudo apt install -y libx11-6 libx11-dev
RUN sudo apt install -y libxrender1 libxrender-dev
RUN sudo apt install -y libxcb1 libx11-xcb-dev
RUN sudo apt install -y libcairo2 libcairo2-dev libxaw7-dev
RUN sudo apt install -y libfl2 libfl-dev zlib1g zlib1g-dev libz-dev libgit2-dev
RUN sudo apt install -y libgoogle-perftools-dev
RUN sudo apt install -y gengetopt groff pod2pdf libhpdf-dev
RUN sudo apt install -y libfftw3-dev
RUN sudo apt install -y libxml-libxml-perl libgd-perl
RUN sudo apt install -y libsuitesparse-dev gfortran swig libspdlog-dev libeigen3-dev liblemon-dev
# our packages
RUN sudo apt install -y gedit libxext-dev libxft-dev tcl-dev tk-dev libqt5designer5 
RUN sudo apt install -y libqt5multimedia5 libqt5opengl5t64 libqt5printsupport5t64 libqt5sql5t64 libqt5xmlpatterns5 
RUN sudo apt install -y vim-gtk3 wget 

USER root
# Install needed python packages on system
RUN apt-get install -y --no-install-recommends \
python3 python3-dev python3-pip python3-virtualenv python3-venv \
python3-sphinx python3-sphinx-autoapi python3-pandas \
python3-tk python3-pytest python3-psutil \
python3-matplotlib python3-numpy \
&& rm -rf /var/lib/apt/lists/*


USER ${USER_NAME}
#Create a microelectronic tools folder
RUN mkdir ~/cad
#Installing and configuring the IHP 130 nm PDK
RUN cd ~/cad \
&& git clone --branch dev --recurse-submodules https://github.com/IHP-GmbH/IHP-Open-PDK.git \
&& cd IHP-Open-PDK \
&& git checkout dev \
&& echo "# Configuração do IHP-Open-PDK" >> ~/.bashrc \
&& echo "export PDK_ROOT=\$HOME/cad/IHP-Open-PDK" >> ~/.bashrc \
&& echo "export PDK=ihp-sg13g2" >> ~/.bashrc \
&& echo "export KLAYOUT_PATH=\"\$HOME/.klayout:\$PDK_ROOT/\$PDK/libs.tech/klayout\"" >> ~/.bashrc \
&& echo "export KLAYOUT_HOME=\$HOME/.klayout" >> ~/.bashrc \
&& echo "export KLAYOUT_PYTHONPATH=/usr/lib/python3/dist-packages" >> ~/.bashrc

#OpenVAF
RUN cd ~/cad \
&& wget https://openva.fra1.cdn.digitaloceanspaces.com/openvaf_${OPENVAF_VERSION}_linux_amd64.tar.gz \
&& tar -xzf openvaf_${OPENVAF_VERSION}_linux_amd64.tar.gz \
&& sudo mv openvaf /usr/local/bin/ \
&& sudo chmod +x /usr/local/bin/openvaf \
&& rm openvaf_${OPENVAF_VERSION}_linux_amd64.tar.gz

#Xschem
RUN export USER=$USER_NAME \
&& export PDK_ROOT=$HOME/cad/IHP-Open-PDK \
&& export PDK=ihp-sg13g2 \
&& cd ~/cad \
&& git clone https://github.com/StefanSchippers/xschem.git \
&& cd xschem \
&& ./configure --prefix=/usr/local \
&& make \
&& sudo make install \
&& cd $PDK_ROOT/$PDK/libs.tech/xschem/ \
&& python3 install.py \
&& cd ~/cad \
&& rm -rf xschem \
&& mkdir -p ~/.xschem \
&& cp $PDK_ROOT/$PDK/libs.tech/xschem/xschemrc ~/.xschem/xschemrc

#Ngspice
RUN export USER=$USER_NAME \
&& export PDK_ROOT=$HOME/cad/IHP-Open-PDK \
&& export PDK=ihp-sg13g2 \
&& cd ~/cad \
#&& git clone https://git.code.sf.net/p/ngspice/ngspice ngspice \
&& git clone --depth 1 --branch ${NGSPICE_BRANCH} https://git.code.sf.net/p/ngspice/ngspice ngspice \
&& cd ngspice \
&& ./autogen.sh \
&& ./configure --enable-osdi --enable-pss --prefix=/usr/local \
&& make \
&& sudo make install \
&& cd .. \
&& rm -rf ngspice

#Magic
RUN cd ~/cad \
&& git clone git://opencircuitdesign.com/magic \
&& cd magic \
&& ./configure --prefix=/usr/local \
&& make \
&& sudo make install \
&& cd .. \
&& rm -rf magic

#KLayout
RUN cd ~/cad \
&& wget https://www.klayout.org/downloads/Ubuntu-24/klayout_${KLAYOUT_VERSION}_amd64.deb \
&& sudo dpkg -i klayout_${KLAYOUT_VERSION}_amd64.deb \
&& rm klayout_${KLAYOUT_VERSION}_amd64.deb

#Netgen
RUN cd ~/cad \
&& git clone git://opencircuitdesign.com/netgen \
&& cd netgen \
&& ./configure --prefix=/usr/local \
&& make \
&& sudo make install \
&& cd .. \
&& rm -rf netgen

#SSH configuration
USER root
RUN apt-get update && apt-get install -y --no-install-recommends openssh-server && rm -rf /var/lib/apt/lists/*
RUN mkdir /run/sshd
RUN echo "X11Forwarding yes" >> /etc/ssh/sshd_config
RUN echo "X11DisplayOffset 10" >> /etc/ssh/sshd_config
#RUN sed -i "s/^#Port 22/Port ${CONTAINER_PORT_ON_HOST}/" /etc/ssh/sshd_config 
RUN echo "UseDNS no" >> /etc/ssh/sshd_config
RUN rm -rf /run/nologin
RUN ssh-keygen -A

#Expose container port for SSH
EXPOSE 22

#SSH service at the start-up
CMD ["/usr/sbin/sshd", "-D"]
