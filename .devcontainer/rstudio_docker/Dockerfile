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

EXPOSE 8787

RUN sudo /rocker_scripts/install_pandoc.sh
RUN sudo /rocker_scripts/install_quarto.sh
RUN sudo /rocker_scripts/install_rstudio.sh

# Create a non-root user
#RUN addgroup --system vscode \
#    && adduser --system --ingroup vscode vscode
#RUN echo "vscode:vscode" |  chpasswd 
# (echo user; echo password) | passwd
#WORKDIR /home/vscode
#COPY index.R           .
# Switch to app user
#RUN chown vscode:vscode -R /home/vscode
# USER vscode

CMD ["/init"]
