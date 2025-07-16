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
# Ensure the non-root user 'pn' and its group exist, then set permissions.
RUN \
    # 1. Create group 'pn' only if it doesn't already exist.
    if ! getent group pn >/dev/null; then groupadd -r pn; fi && \
    \
    # 2. Create user 'pn' only if it doesn't already exist.
    if ! getent passwd pn >/dev/null; then useradd --system --gid pn --no-create-home --home /data --shell /bin/false pn; fi && \
    \
    # 3. Create necessary directories.
    mkdir -p /data /.n8n && \
    \
    # 4. Set ownership to the 'pn' user and group.
    chown -R pn:pn /data /.n8n

# --- Copy Artifacts from Builder Stage ---
COPY --from=builder /usr/local/lib/node_modules /usr/local/lib/node_modules
COPY --from=builder /usr/local/bin /usr/local/bin
COPY --from=builder /usr/local/lib/python3.11/dist-packages /usr/local/lib/python3.11/dist-packages

# --- Environment Variables ---
ENV N8N_DEFAULT_LOCALE=zh-CN
ENV NODE_ICU_DATA=/usr/local/lib/node_modules/full-icu
# Add .n8n to home for user
ENV N8N_USER_FOLDER=/.n8n

# --- Entrypoint ---
COPY docker-entrypoint.sh /docker-entrypoint.sh
RUN chmod +x /docker-entrypoint.sh

# --- Switch to non-privileged user ---
USER pn

WORKDIR /data
ENTRYPOINT ["/docker-entrypoint.sh"]
EXPOSE 5678/tcp
