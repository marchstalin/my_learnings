FROM gitlab/gitlab-ce:latest
ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
  && apt-get install -y --no-install-recommends python3-pip \
  && pip3 install --no-cache-dir ansible \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/*

EXPOSE 80 443 22
