FROM codercom/enterprise-base:ubuntu

# Run everything as root
USER root

##############
#  Runtimes  #
##############

# ========= Node.js (LTS) =========
RUN curl -sL https://deb.nodesource.com/setup_lts.x | bash - && \
    DEBIAN_FRONTEND="noninteractive" apt-get update -y && \
    apt-get install -y nodejs

# ========= pnpm =========
RUN corepack enable && corepack prepare pnpm@10.33.0 --activate
ENV PNPM_HOME="/home/coder/.local/share/pnpm"
ENV PATH="${PNPM_HOME}:${PATH}"
RUN mkdir -p "${PNPM_HOME}" && chown -R coder:coder "${PNPM_HOME}"

# ========= Go 1.25 =========
ARG GO_VERSION=1.25.0
RUN curl -fsSL "https://go.dev/dl/go${GO_VERSION}.linux-amd64.tar.gz" | tar -C /usr/local -xz
ENV PATH="/usr/local/go/bin:${PATH}"
ENV GOPATH="/home/coder/go"
ENV PATH="${GOPATH}/bin:${PATH}"

# ========= golang-migrate CLI =========
RUN curl -fsSL https://github.com/golang-migrate/migrate/releases/download/v4.19.1/migrate.linux-amd64.tar.gz | tar -xz -C /usr/local/bin

# ========= Rust =========
ENV PATH="/home/coder/.cargo/bin:${PATH}"
USER coder
RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | bash -s -- -y
USER root

###########
#  Tools  #
###########

# ========= Database clients (psql, redis-cli) =========
RUN apt-get update && apt-get install -y --no-install-recommends postgresql-client redis-tools \
    && rm -rf /var/lib/apt/lists/*

# ========= GitHub CLI =========
RUN (type -p wget >/dev/null || (apt-get update && apt-get install -y wget)) \
    && mkdir -p -m 755 /etc/apt/keyrings \
    && wget -nv -O /etc/apt/keyrings/githubcli-archive-keyring.gpg https://cli.github.com/packages/githubcli-archive-keyring.gpg \
    && chmod go+r /etc/apt/keyrings/githubcli-archive-keyring.gpg \
    && echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" > /etc/apt/sources.list.d/github-cli.list \
    && apt-get update \
    && apt-get install -y gh \
    && rm -rf /var/lib/apt/lists/*

# ========= Doppler CLI =========
RUN apt-get update && apt-get install -y apt-transport-https ca-certificates curl gnupg \
    && curl -sLf --retry 3 --tlsv1.2 --proto "=https" 'https://packages.doppler.com/public/cli/gpg.DE2A7741A397C129.key' | gpg --dearmor -o /usr/share/keyrings/doppler-archive-keyring.gpg \
    && echo "deb [signed-by=/usr/share/keyrings/doppler-archive-keyring.gpg] https://packages.doppler.com/public/cli/deb/debian any-version main" | tee /etc/apt/sources.list.d/doppler-cli.list \
    && apt-get update && apt-get install doppler

# ========= Fly.io CLI =========
USER coder
RUN curl -L https://fly.io/install.sh | sh -s -- --non-interactive --setup-path
USER root

# ========= Claude Code =========
ENV PATH="/home/coder/.local/bin:${PATH}"
ENV NPM_CONFIG_PREFIX="/home/coder/.local"
RUN mkdir -p /home/coder/.local/bin /home/coder/.claude && \
    chown -R coder:coder /home/coder/.local /home/coder/.claude
USER coder
RUN curl -fsSL https://claude.ai/install.sh | bash

# ========= Codex CLI =========
RUN mkdir -p /home/coder/.codex && \
    chown -R coder:coder /home/coder/.codex
RUN npm install -g @openai/codex

# ========= Gemini CLI =========
RUN mkdir -p /home/coder/.gemini && \
    chown -R coder:coder /home/coder/.gemini
RUN npm install -g @google/gemini-cli

# ========= GitHub Copilot CLI =========
RUN mkdir -p /home/coder/.copilot && \
    chown -R coder:coder /home/coder/.copilot
RUN curl -fsSL https://gh.io/copilot-install | bash || true

# Set back to coder user
USER coder
