FROM codercom/enterprise-base:ubuntu

# Run everything as root
USER root

# Install Node.js (LTS)
RUN curl -sL https://deb.nodesource.com/setup_lts.x | bash - && \
    DEBIAN_FRONTEND="noninteractive" apt-get update -y && \
    apt-get install -y nodejs

# Set back to coder user
USER coder
