###################################################################################################################
# Adapted from https://github.com/microsoft/vscode-dev-containers/blob/master/containers/r/.devcontainer/Dockerfile
# licence: MIT
###################################################################################################################

FROM rocker/r-ver:4.2.1

# Options for setup script
ARG USERNAME="root"
ARG INSTALL_ZSH="true"
ARG USER_UID="1000"
ARG USER_GID=$USER_UID
ARG UPGRADE_PACKAGES="true"
ARG INSTALL_OHMYZSH="false"
ARG INSTALL_NONFREE="false"
ARG QUARTO_VERSION=latest

# https://code.visualstudio.com/remote/advancedcontainers/add-nonroot-user
# Create the user
#RUN groupadd --gid $USER_GID $USERNAME \
#    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
    # [Optional] Add sudo support. Omit if you don't need to install software after connecting.
#    && apt-get update \
#    && apt-get install -y sudo \
#    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
#    && chmod 0440 /etc/sudoers.d/$USERNAME


# key dependencies for certain R packages
RUN apt-get update \
    && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends software-properties-common curl wget libssl-dev libxml2-dev libsodium-dev imagemagick libmagick++-dev libgit2-dev libssh2-1-dev zlib1g-dev librsvg2-dev libudunits2-dev libcurl4-openssl-dev python3-pip pandoc libzip-dev libfreetype6-dev libfontconfig1-dev tk libpq5 libxt6 openssh-client openssh-server \
    && apt-get autoremove -y && apt-get clean -y && rm -rf /var/lib/apt/lists/*

COPY library-scripts/common-debian.sh /tmp/library-scripts/
RUN apt-get update && bash /tmp/library-scripts/common-debian.sh ${INSTALL_ZSH} ${USERNAME} ${USER_UID} ${USER_GID} ${UPGRADE_PACKAGES} ${INSTALL_OHMYZSH} ${INSTALL_NONFREE}

# install R packages needed for VSCode interaction and package management
RUN install2.r --error --skipinstalled --ncpus -4 languageserver renv remotes httpgd

# install radian via python and pip3
RUN apt-get update \
    && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends python3-setuptools \
    && python3 -m pip --no-cache-dir install radian
# RUN sudo pip3 install radian

# install dot net core runtime for the R Tools plugin
RUN wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O /tmp/packages-microsoft-prod.deb \
    && dpkg -i /tmp/packages-microsoft-prod.deb

RUN apt-get update \
    && apt-get -y install --no-install-recommends apt-transport-https \
    && apt-get -y install --no-install-recommends dotnet-sdk-3.1

# ensure that the renv package cache env var is accessible in default R installation
RUN sudo echo "RENV_PATHS_CACHE=/renv/cache" >> /usr/local/lib/R/etc/Renviron

# copy the modified .Rprofile template to the renv cache
#COPY library-scripts/.Rprofile-vscode /renv/.Rprofile-vscode

# [Optional] Uncomment this section to add Quarto to the container
RUN sudo /rocker_scripts/install_quarto.sh

# [Optional] Uncomment this section to add Hugo to the container
# Customize version number as appropriate
#RUN curl -L https://github.com/gohugoio/hugo/releases/download/v0.87.0/hugo_extended_0.87.0_Linux-64bit.deb -o /tmp/hugo.deb
#RUN apt-get -y install ./tmp/hugo.deb

# [Optional] Uncomment this section to add addtional system dependencies needed for project
# RUN apt-get update \
#     && export DEBIAN_FRONTEND=noninteractive \
#     && apt-get -y install --no-install-recommends ---packages list----

# [Optional] Uncomment this section for linking a local pusleaudio sound system on a linux host
# to the container.
#RUN apt-get update \
#    && export DEBIAN_FRONTEND=noninteractive \
#    && apt-get -y install --no-install-recommends libpulse0 libasound2 libasound2-plugins pulseaudio-utils
    
# install obs-cli (Go version)
# https://github.com/muesli/obs-cli/releases
#ADD https://github.com/muesli/obs-cli/releases/download/v0.4.0/obs-cli_0.4.0_linux_x86_64.tar.gz /obs-cli-linux.tar.gz
#RUN tar -zxvf obs-cli-linux.tar.gz
#RUN cp /obs-cli /usr/local/bin/obs-cli
#RUN chmod 755 /usr/local/bin/obs-cli

# install obs-cli (Javascript version)
# https://github.com/leafac/obs-cli/releases
#ADD https://github.com/leafac/obs-cli/releases/download/v2.2.3/obs-cli-linux /usr/local/bin/obs-cli-js
#RUN chmod 755 /usr/local/bin/obs-cli-js

# Create a non-root user
# adduser: The user `rstudio-server' already exists with a different UID. Exiting.
RUN usermod -aG sudo vscode
#RUN addgroup --system vscode
#RUN usermod -aG system vscode
# RUN adduser --ingroup vscode --uid 1000 vscode
# RUN echo 'rstudio-server:rstudio-server' | chpasswd
# (echo rstudio-server; echo rstudio-server) | passwd
RUN echo 'vscode:vscode' | chpasswd
RUN (echo vscode; echo vscode) | passwd
RUN chown vscode:vscode -R /home/vscode

# Service 'vscode' 
# RUN sudo adduser ${USERNAME}
# 2022-08-10 10:08:26.291Z: #17 0.908 adduser: The user `jbg' already exists.
# Create a non-root user
# RUN sudo addgroup --system vscode 
# RUN sudo adduser --system --ingroup staff vscode
#RUN sudo echo "vscode:vscode" |  sudo chpasswd 
# (echo user; echo password) | passwd
# WORKDIR /home/vscode
#COPY index.R           .
# Switch to app user

# [Optional] Set the default user. Omit if you want to keep the default as root.
# USER $USERNAME

# During debugging, this entry point will be overridden. For more information, please refer to https://aka.ms/vscode-docker-python-debug
# CMD ["python", "sample_code_round_data_docker.py"] 