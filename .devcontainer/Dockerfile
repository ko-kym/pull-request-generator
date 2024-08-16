# windows 11
FROM ubuntu:22.04

ARG USER_NAME=vscode
ARG USER_GROUP_NAME=workspace
ARG USER_UID=1000
ARG USER_GID=1000

LABEL maintainer="koko"

# general packages
ARG PKG="vim curl unzip zip sudo tzdata git"

SHELL ["/bin/bash", "-c"]

# ---------------------------------- set up ----------------------------------

# setup timezone https://dev.to/grigorkh/fix-tzdata-hangs-during-docker-image-build-4o9m
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo TZ > /etc/timezone

RUN apt-get update \
    && apt-get install -y ${PKG} \
    && groupadd --gid ${USER_GID} ${USER_GROUP_NAME} \
    && useradd --uid ${USER_UID} --shell /bin/bash --gid ${USER_GID} -m ${USER_NAME} \
    && echo %${USER_GROUP_NAME} ALL=\(ALL\) NOPASSWD:ALL > /etc/sudoers.d/${USER_GROUP_NAME} \
    && chmod 0440 /etc/sudoers.d/${USER_GROUP_NAME}

USER ${USER_NAME}

ENV HOME=/home/${USER_NAME}

WORKDIR ${HOME}

# ---------------------------------- sdkman for java ----------------------------------
# https://sdkman.io/install
ARG JAVA_VERSION=21.0.4-amzn
ARG GRADLE_VERSION=8.10

RUN curl -s "https://get.sdkman.io" | bash \
    && source "${HOME}/.sdkman/bin/sdkman-init.sh" \
    && sdk install java ${JAVA_VERSION} \
    && sdk install gradle ${GRADLE_VERSION} \
    && java --version


# ---------------------------------- set up z ----------------------------------
RUN git clone https://github.com/rupa/z.git ~/.z-sh \
    && echo '. $HOME/.z-sh/z.sh' >> ~/.bashrc

USER root

# ---------------------------------- set up language ----------------------------------
# setup locale https://stackoverflow.com/a/55077451/13723015
ARG LOCALE=ja_JP.UTF-8 
ENV LOCALE=${LOCALE}
ENV LC_ALL=${LOCALE}
ENV LANG=${LOCALE}

RUN apt-get -y install locales \
    && echo "LC_ALL=${LOCALE}" >> /etc/environment \
    && echo "${LOCALE} UTF-8" >> /etc/locale.gen \
    && echo "LANG=${LOCALE}" > /etc/locale.conf \
    && locale-gen ${LOCALE}

# ---------------------------------- addtional packages ----------------------------------

ARG ADDITONAL_PKG="iputils-ping lsof tree jq uuid-runtime"

RUN apt-get update -y \
    && apt-get install ${ADDITONAL_PKG} -y \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*