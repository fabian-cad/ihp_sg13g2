##########################################################
# Dockerfile for configuring the IHP 130 nm CAD tools     #
##########################################################
# The main idea of this Dockerfile is to set up an Ubuntu (24.04)
# environment that can be accessed via SSH on port XXXX (host) → 22 (docker).

#Using ubuntu 24.04
FROM ubuntu:24.04

# Update and dependency installation
RUN apt update
RUN apt install -y sudo

# Admin user
RUN useradd -m -s /bin/bash caduser
RUN echo "caduser ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
RUN echo "caduser:cad1234" | chpasswd
#RUN usermod -aG wheel caduser
# Root user password
RUN echo 'root:cad1234' | chpasswd

USER caduser
RUN sudo apt install -y gedit build-essential flex bison libx11-dev libxpm-dev libxext-dev libxft-dev tcl-dev tk-dev autoconf libtool libxaw7-dev libreadline-dev xterm libqt5designer5 libqt5multimedia5 libqt5opengl5t64 libqt5multimediawidgets5 libqt5printsupport5t64 libqt5sql5t64 libqt5xmlpatterns5 ruby ruby-dev libgit2-dev python3-venv python3-tk vim-gtk3
RUN sudo apt install -y git
RUN sudo apt install -y wget 

#Set up a Python virtual environment for the tools
RUN python3 -m venv $HOME/.venv \
&& echo "# Ambiente virtual Python ativado por padrão" >> ~/.bashrc \
&& echo "# para desativar, executar comando 'deactivate'" >> ~/.bashrc \
&& echo "if [ -f "$HOME/.venv/bin/activate" ]; then" >> ~/.bashrc \
&& echo "source "$HOME/.venv/bin/activate"" >> ~/.bashrc \
&& echo "fi" >> ~/.bashrc \
&& $HOME/.venv/bin/pip install --upgrade pip \
&& $HOME/.venv/bin/pip install psutil \
&& $HOME/.venv/bin/pip install matplotlib \
&& $HOME/.venv/bin/pip install numpy

#Create a microelectronic tools folder
RUN mkdir ~/cad

#Installing and configuring the IHP 130 nm PDK
RUN cd ~/cad \
&& git clone --branch dev --recurse-submodules https://github.com/IHP-GmbH/IHP-Open-PDK.git \
&& cd IHP-Open-PDK \
&& echo "# Configuração do IHP-Open-PDK" >> ~/.bashrc \
&& echo "export PDK_ROOT=\$HOME/cad/IHP-Open-PDK" >> ~/.bashrc \
&& echo "export PDK=ihp-sg13g2" >> ~/.bashrc \
&& echo "export KLAYOUT_PATH=\"\$HOME/.klayout:\$PDK_ROOT/\$PDK/libs.tech/klayout\"" >> ~/.bashrc \
&& echo "export KLAYOUT_HOME=\$HOME/.klayout" >> ~/.bashrc \
&& echo "export KLAYOUT_PYTHONPATH=\$HOME/.venv/lib/python3.12/site-packages" >> ~/.bashrc

#OpenVAF
RUN cd ~/cad \
&& wget https://openva.fra1.cdn.digitaloceanspaces.com/openvaf_23_5_0_linux_amd64.tar.gz \
&& tar -xzf openvaf_23_5_0_linux_amd64.tar.gz \
&& sudo mv openvaf /usr/local/bin/ \
&& sudo chmod +x /usr/local/bin/openvaf \
&& rm openvaf_23_5_0_linux_amd64.tar.gz

#Xschem
RUN export USER=caduser \
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
RUN export USER=caduser \
&& export PDK_ROOT=$HOME/cad/IHP-Open-PDK \
&& export PDK=ihp-sg13g2 \
&& cd ~/cad \
&& git clone https://git.code.sf.net/p/ngspice/ngspice ngspice \
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
&& wget https://www.klayout.org/downloads/Ubuntu-24/klayout_0.30.3-1_amd64.deb \
&& sudo dpkg -i klayout_0.30.3-1_amd64.deb \
&& rm klayout_0.30.3-1_amd64.deb

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
RUN apt install -y openssh-server
RUN mkdir /run/sshd \
&& echo "X11Forwarding yes" >> /etc/ssh/sshd_config \
&& echo "X11DisplayOffset 10" >> /etc/ssh/sshd_config \
&& echo "UseDNS no" >> /etc/ssh/sshd_config \
&& rm -rf /run/nologin \
&& ssh-keygen -A

#Expose container port 22 for SSH
EXPOSE 22

#SSH service at the start-up
CMD ["/usr/sbin/sshd", "-D"]





