# Build final docker image now that all binaries are OK
FROM ubuntu:kinetic as base

ARG TERRAFORM_VERSION
ENV TERRAFORM_VERSION $TERRAFORM_VERSION
ARG TERRAGRUNT_VERSION
ENV TERRAGRUNT_VERSION $TERRAGRUNT_VERSION

ARG DEBIAN_FRONTEND=noninteractive
ENV TZ=Europe/Paris

# Install ubuntu packages
RUN apt update -y
RUN apt install -y jq curl wget zip python3 python3-pip git tar gzip
RUN apt clean -y
RUN apt autoremove -y

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
RUN aws --version

# Entrypoint
ENTRYPOINT ["/bin/bash"]

# Test the image before building
FROM base AS test

RUN terraform -v && \
    terragrunt -v && \
    aws --version

# Create Image after tests
FROM base AS release