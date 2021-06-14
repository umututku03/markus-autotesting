ARG UBUNTU_VERSION

FROM ubuntu:$UBUNTU_VERSION as base

ARG LOGIN_USER

RUN apt-get update -y && \
    apt-get -y install software-properties-common && \
    add-apt-repository -y ppa:deadsnakes/ppa && \
    apt-get -y install python3.6 \
                       python3.6-venv \
                       python3.7 \
                       python3.7-venv \
                       python3.8 \
                       python3.8-venv \
                       python3.9 \
                       python3.9-venv \
                       redis-server \
                       postgresql-client \
                       libpq-dev \
                       sudo \
                       git

RUN useradd -ms /bin/bash $LOGIN_USER && \
    usermod -aG sudo $LOGIN_USER && \
    for worker in autotst0 autotst1 autotst2 autotst3; do \
        adduser --disabled-login --no-create-home $worker && \
        echo "$LOGIN_USER ALL=($worker) NOPASSWD:ALL" | EDITOR="tee -a" visudo && \
        usermod -aG $worker $LOGIN_USER; \
    done

COPY . /app

RUN python3.9 -m venv /markus_venv && \
    /markus_venv/bin/pip install -r /app/requirements.txt && \
    find /app/autotest_server/testers -name requirements.system -exec {} \; && \
    rm -rf /app/*

WORKDIR /home/${LOGIN_USER}

USER ${LOGIN_USER}

CMD /app/.dockerfiles/cmd-dev.sh