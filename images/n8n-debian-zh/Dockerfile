FROM nikolaik/python-nodejs:python3.11-nodejs22

ARG N8N_VERSION

RUN if [ -z "$N8N_VERSION" ] ; then echo "The N8N_VERSION argument is missing!" ; exit 1; fi

# 系統包安裝
RUN apt-get update && \
    apt-get -y install \
    graphicsmagick \
    gosu \
    git \
    python3-dev \
    gcc \
    g++ \
    build-essential \
    curl \
    tar \
    gzip \
    jq && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# Python 依賴
COPY requirements.txt /requirements/requirements.txt
RUN python -m pip install --upgrade pip setuptools wheel && \
    pip install -r /requirements/requirements.txt

# 確保使用 root 進行全局安裝
USER root

# npm 全局安裝
RUN npm install -g full-icu n8n@${N8N_VERSION} && \
    cd /usr/local/lib/node_modules/n8n && \
    npm install n8n-nodes-python n8n-nodes-changed n8n-nodes-updates n8n-nodes-influxdb n8n-nodes-tavily

# 中文化包安裝（保持原有邏輯）
RUN PURE_N8N_VERSION=${N8N_VERSION#n8n@} && \
    echo "正在為 n8n 版本 ${PURE_N8N_VERSION} 嘗試下載對應的中文化包..." && \
    ( \
        mkdir -p /tmp/n8n-i18n && \
        curl -fL -o /tmp/n8n-i18n/editor-ui.tar.gz "https://github.com/other-blowsnow/n8n-i18n-chinese/releases/download/n8n%40${PURE_N8N_VERSION}/editor-ui.tar.gz" && \
        echo "中文化包下載成功，正在安裝..." && \
        mkdir -p /usr/local/lib/node_modules/n8n/node_modules/n8n-editor-ui/dist && \
        tar -xzf /tmp/n8n-i18n/editor-ui.tar.gz -C /usr/local/lib/node_modules/n8n/node_modules/n8n-editor-ui/dist \
    ) || echo "警告：未找到版本 ${PURE_N8N_VERSION} 對應的中文化包，將使用預設英文介面。" && \
    rm -rf /tmp/n8n-i18n

# 環境變數設定
ENV N8N_DEFAULT_LOCALE=zh-CN
ENV PYTHONPATH=/usr/local/lib/python3.11/dist-packages:/home/pyodide/lib/python3.11/site-packages
ENV NODE_ICU_DATA=/usr/local/lib/node_modules/full-icu

# 創建工作目錄並設定權限
RUN mkdir -p /data && \
    chown -R pn:pn /data

# 切換到非特權用戶
USER pn

WORKDIR /data

COPY docker-entrypoint.sh /docker-entrypoint.sh
RUN chmod +x /docker-entrypoint.sh
ENTRYPOINT ["/docker-entrypoint.sh"]

EXPOSE 5678/tcp
