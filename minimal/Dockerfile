# Build final docker image now that all binaries are OK
FROM ubuntu:kinetic as base

ARG TERRAFORM_VERSION
ENV TERRAFORM_VERSION $TERRAFORM_VERSION
ARG TERRAGRUNT_VERSION
ENV TERRAGRUNT_VERSION $TERRAGRUNT_VERSION

ARG DEBIAN_FRONTEND=noninteractive
ENV TZ=Europe/Paris

# Make /bin/sh symlink to bash instead of dash:
RUN echo "dash dash/sh boolean false" | debconf-set-selections
RUN DEBIAN_FRONTEND=noninteractive dpkg-reconfigure dash
RUN rm /bin/sh && ln -s /bin/bash /bin/sh
SHELL ["/bin/bash", "-c"]

# Install ubuntu packages
RUN apt update -y
RUN apt install -y --no-install-recommends jq curl wget zip unzip python3-minimal git tar gzip apt-transport-https ca-certificates
RUN update-ca-certificates
RUN apt autoremove -y
RUN apt purge -y --auto-remove
RUN apt clean -y

# Install terraform
COPY files/terraform_linux_amd64.zip /root/terraform_linux_amd64.zip
RUN unzip /root/terraform_linux_amd64.zip -d /root
RUN rm /root/terraform_linux_amd64.zip
RUN mv /root/terraform /usr/local/bin/terraform
RUN chmod +x /usr/local/bin/terraform
RUN terraform -v

# Install terragrunt
COPY files/terragrunt /usr/local/bin/terragrunt
RUN chmod +x /usr/local/bin/terragrunt
RUN terragrunt -v

# Install AWS CLI V2
COPY files/awscli.zip /root/awscli.zip
RUN unzip /root/awscli.zip -d /root
RUN rm /root/awscli.zip
RUN /root/aws/install
RUN rm -rf /usr/local/aws-cli/v2/current/dist/aws_completer /usr/local/aws-cli/v2/current/dist/awscli/data/ac.index /usr/local/aws-cli/v2/current/dist/awscli/examples
RUN find /usr/local/aws-cli/v2/current/dist/awscli/botocore/data -name examples-1.json -delete
RUN aws --version

# Entrypoint
ENTRYPOINT ["/bin/bash", "-l", "-c"]

# Test the image before building
FROM base AS test

RUN terraform -v && \
    terragrunt -v && \
    aws --version

# Create Image after tests
FROM base AS release
