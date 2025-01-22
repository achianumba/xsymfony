ARG VARIANT=8.3-apache

FROM php:${VARIANT}

ARG DEBIAN_FRONTEND=noninteractive

# Install packages
RUN apt update \
    && apt -y upgrade \
    && apt -y install \
    sudo \
    curl \
    wget \
    zip \
    unzip \
    bzip2 \
    ssh-client \
    apt-transport-https \
    gnupg \
    git \
    postgresql-client \
    httpie

# Install composer and PHP extensions
COPY --from=composer /usr/bin/composer /usr/bin/composer
COPY --from=mlocati/php-extension-installer /usr/bin/install-php-extensions /usr/local/bin/

# and PHP extensions & Symfony CLI 
RUN install-php-extensions \
    intl \
    pdo_mysql \
    sockets \
    pdo_pgsql \
    xsl \
    amqp \
    gd \
    openssl \
    sodium \
    redis \
    curl \
    zip \
    ctype \
    iconv \
    pcre \
    session \
    xml \
    tokenizer \
    && curl -1sLf 'https://dl.cloudsmith.io/public/symfony/stable/setup.deb.sh' | bash \
    && apt -y install symfony-cli \
    && apt-get autoremove \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

ARG USERNAME=xsymfony
ARG USER_UID=1000
ARG USER_GID=${USER_UID}

# Create regular user
RUN groupadd -g ${USER_GID} ${USERNAME} \ 
    && useradd -g ${USER_GID} -u ${USER_UID} -ms /usr/bin/bash ${USERNAME} \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME

USER ${USERNAME}

ARG USER_HOME=/home/${USERNAME}

ENV NVM_DIR="${USER_HOME}/.nvm"

# Install Node Version Manager
RUN echo 'lts/*' > "${USER_HOME}/.nvmrc" \
    && git clone https://github.com/nvm-sh/nvm.git "${NVM_DIR}" \
    && cd ${NVM_DIR} \
    && git checkout `git describe --abbrev=0 --tags --match "v[0-9]*" $(git rev-list --tags --max-count=1)` \
    && echo '\. "$NVM_DIR/nvm.sh"' >> /${USER_HOME}/.profile \
    && echo '\. "$NVM_DIR/nvm.sh"' >> /${USER_HOME}/.bashrc \
    && echo '\. "$NVM_DIR/bash_completion"' >> /${USER_HOME}/.profile \
    && echo '\. "$NVM_DIR/bash_completion"' >> /${USER_HOME}/.bashrc

# Install Node LTS
RUN \. "$NVM_DIR/nvm.sh" \
    && nvm install lts/* \
    && touch /${USER_HOME}/.profile /${USER_HOME}/.bashrc

RUN mkdir -p ${USER_HOME}/app

WORKDIR /${USER_HOME}/app