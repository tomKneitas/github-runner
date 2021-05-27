FROM ubuntu:20.04

ARG TARGETPLATFORM=x86_64
ARG RUNNER_VERSION=2.278.0
ARG DOCKER_VERSION=19.03.12

ENV GITHUB_PAT ""
ENV GITHUB_TOKEN ""
ENV GITHUB_OWNER ""
ENV GITHUB_REPOSITORY ""
ENV RUNNER_WORKDIR "_work"
ENV RUNNER_LABELS ""
ENV ADDITIONAL_PACKAGES ""

RUN apt-get update && apt-get install --no-install-recommends -y \
    apt-transport-https \
    curl \
    sudo \
    git \
    jq \
    iputils-ping \
    ca-certificates \
    gnupg-agent &&
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg |
    apt-key add - &&
    apt-key fingerprint 0EBFCD88 &&
    add-apt-repository \
        "deb [arch=amd64] https://download.docker.com/linux/ubuntu     \
       $(lsb_release -cs)                                             \
       stable" &&
    apt-get update && apt-get install --no-install-recommends -y \
    docker-ce docker-ce-cli containerd.io &&

    # Housekeeping
    apt-get clean -y &&
    rm -rf \
        /var/cache/debconf/* \
        /var/lib/apt/lists/* \
        /var/log/* \
        /tmp/* \
        /var/tmp/* \
        /usr/share/doc/* \
        /usr/share/man/* \
        /usr/share/local/* &&

    # Add user "admin" to the Docker group
    useradd --create-home --shell /bin/bash runner && echo "runner:runner" | chpasswd && adduser admin sudo && usermod -a -G docker runner

USER runner
WORKDIR /home/github

RUN curl -Ls https://github.com/actions/runner/releases/download/v${RUNNER_VERSION}/actions-runner-linux-x64-${RUNNER_VERSION}.tar.gz | tar xz &&
    sudo ./bin/installdependencies.sh

COPY --chown=runner:runner entrypoint.sh ./entrypoint.sh
RUN sudo chmod u+x ./entrypoint.sh

ENTRYPOINT ["/home/github/entrypoint.sh"]
