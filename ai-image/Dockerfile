FROM mcr.microsoft.com/devcontainers/base:ubuntu

# --- ROOT CONTEXT ---

# 1. Install System Dependencies
RUN apt-get update && apt-get install -y \
    curl \
    wget \
    gpg \
    git \
    tmux \
    vim \
    software-properties-common \
    sudo \
    && rm -rf /var/lib/apt/lists/*

# 2. Install GitHub CLI (Always fetches latest from repo)
RUN mkdir -p -m 755 /etc/apt/keyrings && \
    curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | \
    gpg --dearmor -o /etc/apt/keyrings/githubcli-archive-keyring.gpg && \
    chmod go+r /etc/apt/keyrings/githubcli-archive-keyring.gpg && \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | \
    tee /etc/apt/sources.list.d/github-cli.list > /dev/null && \
    apt-get update && apt-get install -y gh

# 3. Install Node.js (Current/Latest)
RUN curl -fsSL https://deb.nodesource.com/setup_current.x | bash - && \
    apt-get install -y nodejs

# 4. Install Global AI Tools
RUN npm install -g @google/gemini-cli @openai/codex @anthropic-ai/claude-code

# 5. Install Java 25 & Maven
# A. Install Java 25 (Temurin) via Adoptium
RUN mkdir -p /etc/apt/keyrings && \
    wget -O - https://packages.adoptium.net/artifactory/api/gpg/key/public | tee /etc/apt/keyrings/adoptium.asc && \
    echo "deb [signed-by=/etc/apt/keyrings/adoptium.asc] https://packages.adoptium.net/artifactory/deb $(awk -F= '/^VERSION_CODENAME/{print$2}' /etc/os-release) main" | tee /etc/apt/sources.list.d/adoptium.list && \
    apt-get update && \
    apt-get install -y temurin-25-jdk

# B. Install Maven (Manual Install to keep JDK clean)
ENV MAVEN_VERSION=3.9.12
ENV MAVEN_HOME=/usr/share/maven
RUN mkdir -p /usr/share/maven && \
    curl -fsSL https://downloads.apache.org/maven/maven-3/${MAVEN_VERSION}/binaries/apache-maven-${MAVEN_VERSION}-bin.tar.gz | tar -xzC /usr/share/maven --strip-components=1 && \
    ln -s /usr/share/maven/bin/mvn /usr/bin/mvn

# C. Setup Java Environment Variables (DYNAMIC FIX)
# We find the installed Temurin folder (regardless of arch name) and symlink it to a static path
RUN export INSTALLED_JAVA_DIR=$(find /usr/lib/jvm -maxdepth 1 -name "temurin-25-jdk-*" -type d | head -n 1) && \
    ln -s "$INSTALLED_JAVA_DIR" /usr/lib/jvm/custom-java-home
ENV JAVA_HOME=/usr/lib/jvm/custom-java-home    
ENV PATH="${JAVA_HOME}/bin:${MAVEN_HOME}/bin:${PATH}"

# 6. Setup User Sudo
RUN echo "vscode ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/vscode \
    && chmod 0440 /etc/sudoers.d/vscode

# 7. Create Self-Healing Entrypoint Script
# We create this file NOW, but it runs LATER (on container start).
# It forces the mounted workspace to be owned by vscode.
RUN printf '#!/bin/bash\n\
sudo chown -R vscode:vscode /workspace\n\
exec "$@"\n' > /usr/local/bin/entrypoint.sh \
    && chmod +x /usr/local/bin/entrypoint.sh

# --- USER CONTEXT ---

USER vscode

# 8. Git Config
RUN git config --global user.name "Radu C" && \
    git config --global user.email "radu.cirstoiu@gmail.com"

# 9. Environment Variables
ENV TERM=xterm

# 10. Permissions & Workdir
WORKDIR /workspace

# 11. Entrypoint & Command
# This script runs first to fix permissions, then passes control to CMD
ENTRYPOINT ["/usr/local/bin/entrypoint.sh"]
CMD ["sleep", "infinity"]
