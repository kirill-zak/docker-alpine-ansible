# Get version of Alpine Linux to build
ARG ALPINE_VERSION

# Use Alpine Linux Base image
FROM alpine:${ALPINE_VERSION}
LABEL maintainer="Kirill Ziuzin"
ENV container=docker

# Install python
RUN apk add --update --no-cache \
            python3 \
            openrc \
            ansible \
            sudo && \
            ln -sf python3 /usr/bin/python

# Disable requiretty.
RUN sed -i -e 's/^\(Defaults\s*requiretty\)/#--- \1/'  /etc/sudoers

# Install Ansible inventory file.
RUN mkdir -p /etc/ansible
RUN echo -e '[local]\nlocalhost ansible_connection=local ansible_python_interpreter=/usr/bin/python3' > /etc/ansible/hosts

VOLUME ["/sys/fs/cgroup", "/tmp", "/run"]

CMD ["/sbin/init"]
