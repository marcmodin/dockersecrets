# syntax = docker/dockerfile:1.0-experimental
FROM ubuntu:bionic

# To make it easier for build and release pipelines to run apt-get,
# configure apt to not require confirmation (assume the -y argument by default)
ENV DEBIAN_FRONTEND=noninteractive
ENV AZP_URL=https://dev.azure.com/marcmodin0206/
ENV AGENT_ALLOW_RUNASROOT="1"

RUN echo "APT::Get::Assume-Yes \"true\";" > /etc/apt/apt.conf.d/90assumeyes

RUN ["/bin/bash", "-c", "apt-get update && apt-get install -y --no-install-recommends \
  ca-certificates \
  curl \
  jq \
  git \
  iputils-ping \
  libcurl4 \
  libicu60 \
  libunwind8 \
  netcat \
  && rm -rf /var/lib/apt/lists/* \
  "]

WORKDIR /azp
COPY ./install.sh .

RUN --mount=type=secret,id=mysecret cat /run/secrets/mysecret > .token \
  && export AZP_TOKEN_FILE=/azp/.token \
  && chmod +x install.sh \
  &&./install.sh \
  && rm -f .token \
  && rm -f install.sh


# CMD ["./config.sh"]