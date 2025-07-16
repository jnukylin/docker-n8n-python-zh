# ############# #
#  Builder Stage  #
# ############# #
FROM nikolaik/python-nodejs:python3.11-nodejs22 as builder

# --- Build Arguments ---
ARG N8N_VERSION
ARG N8N_PLUGINS="n8n-nodes-python n8n-nodes-changed n8n-nodes-updates n8n-nodes-influxdb n8n-nodes-tavily"

# --- Validate Arguments ---
RUN if [ -z "$N8N_VERSION" ] ; then echo "The N8N_VERSION argument is missing!" ; exit 1; fi

# --- Install Build-time System Dependencies ---
RUN apt-get update && \
    apt-get -y install --no-install-recommends \
    python3-dev \
    gcc \
    g++ \
    build-essential \
    curl \
    tar \
    gzip && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# --- Install Python Dependencies ---
COPY requirements.txt /requirements/requirements.txt
RUN python -m pip install --upgrade pip setuptools wheel && \
    pip install --no-cache-dir -r /requirements/requirements.txt

# --- Install n8n and plugins ---
RUN npm install -g --production \
    full-icu \
    n8n@${N8N_VERSION} \
    ${N8N_PLUGINS}

# --- Install Chinese Language Pack ---
RUN PURE_N8N_VERSION=${N8N_VERSION#n8n@} && \
    echo "Attempting to download Chinese pack for n8n version ${PURE_N8N_VERSION}..." && \
    ( \
        mkdir -p /tmp/n8n-i18n && \
        curl -fL -o /tmp/n8n-i18n/editor-ui.tar.gz "https://github.com/other-blowsnow/n8n-i18n-chinese/releases/download/n8n%40${PURE_N8N_VERSION}/editor-ui.tar.gz" && \
        echo "Chinese pack downloaded successfully. Installing..." && \
        mkdir -p /usr/local/lib/node_modules/n8n/node_modules/n8n-editor-ui/dist && \
        tar -xzf /tmp/n8n-i18n/editor-ui.tar.gz -C /usr/local/lib/node_modules/n8n/node_modules/n8n-editor-ui/dist \
    ) || echo "Warning: Chinese pack for version ${PURE_N8N_VERSION} not found. Using default English UI." && \
    rm -rf /tmp/n8n-i18n

# ################## #
#  Final Stage       #
# ################## #
FROM nikolaik/python-nodejs:python3.11-nodejs22

# --- Install Run-time System Dependencies ---
RUN apt-get update && \
    apt-get -y install --no-install-recommends \
    graphicsmagick \
    gosu \
    jq && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# --- User and Group Setup ---
# Use the default 'node' user from the base image if it exists, or create 'pn'
RUN if ! id "node" >/dev/null 2>&1; then \
        groupadd -r pn && useradd -r -g pn -d /data -s /bin/bash pn; \
    fi && \
    mkdir -p /data /.n8n && \
    chown -R $(id -u node 2>/dev/null || echo "pn"):$(id -g node 2>/dev/null || echo "pn") /data /.n8n

# --- Copy Artifacts from Builder Stage ---
COPY --from=builder /usr/local/lib/node_modules /usr/local/lib/node_modules
COPY --from=builder /usr/local/bin /usr/local/bin
COPY --from=builder /usr/local/lib/python3.11/dist-packages /usr/local/lib/python3.11/dist-packages

# --- Environment Variables ---
ENV N8N_DEFAULT_LOCALE=zh-CN
ENV NODE_ICU_DATA=/usr/local/lib/node_modules/full-icu
# Add .n8n to home for user
ENV N8N_USER_FOLDER=/data/.n8n

# --- Entrypoint ---
COPY docker-entrypoint.sh /docker-entrypoint.sh
RUN chmod +x /docker-entrypoint.sh

# --- Switch to non-privileged user ---
USER $(id -u node 2>/dev/null || echo "pn")

WORKDIR /data
ENTRYPOINT ["/docker-entrypoint.sh"]
EXPOSE 5678/tcp
