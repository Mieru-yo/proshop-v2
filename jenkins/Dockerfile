FROM jenkins/jenkins:lts-jdk17

USER root

RUN apt-get update \
    && apt-get install -y --no-install-recommends docker.io curl ca-certificates \
    && mkdir -p /usr/lib/docker/cli-plugins \
    && curl -fsSL https://github.com/docker/compose/releases/download/v2.29.7/docker-compose-linux-x86_64 \
      -o /usr/lib/docker/cli-plugins/docker-compose \
    && chmod +x /usr/lib/docker/cli-plugins/docker-compose \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

USER jenkins
