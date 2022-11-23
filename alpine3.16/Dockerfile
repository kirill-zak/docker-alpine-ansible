# Get version of Alpine Linux to build
ARG ALPINE_VERSION

# Use Alpine Linux Base image
FROM alpine:${ALPINE_VERSION}
LABEL maintainer="Kirill Ziuzin"
ENV container=docker

ENV pip_packages "ansible"

# Install python
RUN apk add --update --no-cache \
            python3 \
            py3-pip \
            openrc \
            sudo && \
            ln -sf python3 /usr/bin/python

# Disable requiretty.
RUN sed -i -e 's/^\(Defaults\s*requiretty\)/#--- \1/'  /etc/sudoers

# Install ansible
RUN python3 -m pip install --no-cache --upgrade pip && pip3 install $pip_packages

# Install Ansible inventory file.
RUN mkdir -p /etc/ansible
RUN echo -e '[local]\nlocalhost ansible_connection=local ansible_python_interpreter=/usr/bin/python3' > /etc/ansible/hosts

VOLUME ["/sys/fs/cgroup", "/tmp", "/run"]

CMD ["/sbin/init"]