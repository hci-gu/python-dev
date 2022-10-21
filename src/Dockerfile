FROM python:3.8.13-buster

# Install Conda miniforge
ARG MINIFORGE_NAME=Miniforge3
ARG MINIFORGE_VERSION=4.14.0-0
ARG TINI_VERSION=v0.18.0
ARG TARGETPLATFORM=arm64

ENV CONDA_DIR=/opt/conda
ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
ENV PATH=${CONDA_DIR}/bin:${PATH}

RUN apt-get update > /dev/null && \
    apt-get install --no-install-recommends --yes \
        wget bzip2 ca-certificates \
        git > /dev/null && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* && \
    TARGETARCH="$(echo ${TARGETPLATFORM} | cut -d / -f 2)"; case ${TARGETARCH} in "ppc64le") TARGETARCH="ppc64el" ;; *) ;; esac ; \
    wget --no-hsts --quiet https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini-${TARGETARCH} -O /usr/local/bin/tini && \
    chmod +x /usr/local/bin/tini && \
    wget --no-hsts --quiet https://github.com/conda-forge/miniforge/releases/download/${MINIFORGE_VERSION}/${MINIFORGE_NAME}-${MINIFORGE_VERSION}-Linux-$(uname -m).sh -O /tmp/miniforge.sh && \
    /bin/bash /tmp/miniforge.sh -b -p ${CONDA_DIR} && \
    rm /tmp/miniforge.sh && \
    conda clean --tarballs --index-cache --packages --yes && \
    find ${CONDA_DIR} -follow -type f -name '*.a' -delete && \
    find ${CONDA_DIR} -follow -type f -name '*.pyc' -delete && \
    conda clean --force-pkgs-dirs --all --yes  && \
    echo ". ${CONDA_DIR}/etc/profile.d/conda.sh && conda activate base" >> /etc/skel/.bashrc && \
    echo ". ${CONDA_DIR}/etc/profile.d/conda.sh && conda activate base" >> ~/.bashrc

# Install Node.js
RUN \
  echo "deb https://deb.nodesource.com/node_16.x buster main" > /etc/apt/sources.list.d/nodesource.list && \
  wget -qO- https://deb.nodesource.com/gpgkey/nodesource.gpg.key | apt-key add - && \
  echo "deb https://dl.yarnpkg.com/debian/ stable main" > /etc/apt/sources.list.d/yarn.list && \
  wget -qO- https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - && \
  apt-get update && \
  apt-get install -yqq nodejs yarn && \
  pip install -U pip && pip install pipenv && \
  npm i -g npm@^8 && \
  curl -sSL https://raw.githubusercontent.com/python-poetry/poetry/master/install-poetry.py | python - && \
  rm -rf /var/lib/apt/lists/*

RUN npm install -g nodemon
