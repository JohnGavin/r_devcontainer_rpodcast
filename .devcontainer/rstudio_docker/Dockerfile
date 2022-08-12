##################################################################################################################################################################
# Adapted from following:
# - Rocker RStudio container using new versioned paradigm: https://github.com/rocker-org/rocker-versioned2/blob/master/dockerfiles/Dockerfile_rstudio_4.0.2
# - license: GPLV2
##################################################################################################################################################################

FROM rocker/r-ver:4.2.1

ARG S6_VERSION
ARG RSTUDIO_VERSION
ARG QUARTO_VERSION

ENV S6_VERSION=$S6_VERSION
ENV RSTUDIO_VERSION=$RSTUDIO_VERSION
ENV PATH=/usr/lib/rstudio-server/bin:$PATH
ENV QUARTO_VERSION=$QUARTO_VERSION

# key dependencies for certain R packages
RUN apt-get update \
    && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends software-properties-common curl wget libssl-dev libxml2-dev libsodium-dev imagemagick libmagick++-dev libgit2-dev libssh2-1-dev zlib1g-dev librsvg2-dev libudunits2-dev libcurl4-openssl-dev python3-pip pandoc libzip-dev libfreetype6-dev libfontconfig1-dev tk libpq5 libxt6 openssh-client openssh-server \
    && apt-get autoremove -y \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/*


# install key dependencies of certain packages that could be installed later
RUN apt-get update \
    && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends libxml2-dev libsodium-dev libssl-dev imagemagick libmagick++-dev libgit2-dev libssh2-1-dev zlib1g-dev librsvg2-dev libudunits2-dev libfontconfig1-dev libfreetype6-dev curl  sudo

RUN sudo echo "RENV_PATHS_CACHE=/renv/cache" >> /usr/local/lib/R/etc/Renviron

# [Optional] Uncomment this section to add addtional system dependencies needed for project
# RUN apt-get update \
#     && export DEBIAN_FRONTEND=noninteractive \
#     && apt-get -y install --no-install-recommends ---packages list----

# [Optional] Uncomment this section to install additional R packages.
RUN sudo install2.r --error --skipinstalled --ncpus -1 renv remotes


RUN sudo /rocker_scripts/install_pandoc.sh
RUN sudo /rocker_scripts/install_quarto.sh
RUN sudo /rocker_scripts/install_rstudio.sh

# Create a non-root user
# RUN addgroup --system rstudio
# adduser: The user `rstudio-server' already exists with a different UID. Exiting.
# RUN adduser --system --ingroup rstudio --uid 1000 rstudio-server
#WORKDIR /home/vscode
#COPY index.R           .
# RUN echo 'rstudio-server:rstudio-server' | chpasswd
# (echo rstudio-server; echo rstudio-server) | passwd
RUN usermod -aG sudo rstudio
RUN echo 'rstudio:rstudio' | chpasswd
RUN (echo rstudio; echo rstudio) | passwd
RUN chown rstudio:rstudio -R /home/rstudio
# --uid 1000 
# adduser: The user `rstudio' already exists, but is not a system user. Exiting.
# RUN adduser --system --ingroup rstudio rstudio
# RUN adduser --ingroup system rstudio
# RUN adduser --ingroup rstudio rstudio
# RUN useradd -m -p rstudio rstudio
# RUN useradd -p rstudio rstudio

# Change the UID/GID of an existing container user#
# https://code.visualstudio.com/remote/advancedcontainers/add-nonroot-user
#ARG USERNAME=user-name-goes-here
#ARG USER_UID=1000
#ARG USER_GID=$USER_UID
#RUN groupmod --gid $USER_GID $USERNAME \
#    && usermod --uid $USER_UID --gid $USER_GID $USERNAME \
#    && chown -R $USER_UID:$USER_GID /home/$USERNAME



# USER vscode
# USER rstudio
# Switch to rstudio-server user:
# else ERROR Attempt to run server as user 'rstudio-server' (uid 999) from account 'rstudio' (uid 1000) without privilege, which is required to run as a different uid
USER rstudio

EXPOSE 8787
# Expose Jupyter port
EXPOSE 8888

CMD ["/init"]
