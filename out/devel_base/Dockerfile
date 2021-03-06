# DO NOT EDIT FILES CALLED 'Dockerfile'; they are automatically
# generated. Edit 'Dockerfile.in' and generate the 'Dockerfile'
# with the 'rake' command.

# The suggested name for this image is: bioconductor/devel_base.
FROM rocker/rstudio:3.6.0

MAINTAINER maintainer@bioconductor.org

# nuke cache dirs before installing pkgs; tip from Dirk E fixes broken img
RUN rm -f /var/lib/dpkg/available && rm -rf  /var/cache/apt/*

# same set of packages for both devel and release
RUN apt-get update && \
	apt-get -y --no-install-recommends install --fix-missing \
	gdb \
	libxml2-dev \
	python-pip \
	libz-dev \
	liblzma-dev \
	libbz2-dev \
	libpng-dev \
	libmariadb-client-lgpl-dev \
	&& rm -rf /var/lib/apt/lists/*

# issues with '/var/lib/dpkg/available' not found
# this will recreate
RUN dpkg --clear-avail

ADD install.R /tmp/

RUN R -f /tmp/install.R

# Add bioc user as requested
RUN useradd -ms /bin/bash -d /home/bioc bioc \
	&& echo "bioc:bioc" | chpasswd && adduser bioc sudo
USER bioc
RUN mkdir -p /home/bioc/R/library && \
	echo "R_LIBS=/usr/local/lib/R/host-site-library:~/R/library" | cat > /home/bioc/.Renviron
USER root
RUN echo "R_LIBS=/usr/local/lib/R/host-site-library:\${R_LIBS}" > /usr/local/lib/R/etc/Renviron.site \
	&& echo "R_LIBS_USER=''" >> /usr/local/lib/R/etc/Renviron.site \
	&& echo "options(defaultPackages=c(getOption('defaultPackages'),'BiocManager'))" >> /usr/local/lib/R/etc/Rprofile.site
