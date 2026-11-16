FROM jenkins/jenkins:lts-jdk17
USER root
# make: para ejecutar los comandos del makefile (make build, make test-unit...)
RUN apt-get update && apt-get install -y make && rm -rf /var/lib/apt/lists/*
# cliente de docker: para que jenkins pueda crear contenedores
COPY --from=docker:27-cli /usr/local/bin/docker /usr/local/bin/docker
COPY --from=docker:27-cli /usr/local/libexec/docker/cli-plugins /usr/local/libexec/docker/cli-plugins