# 使用指定的 Python 和 Node.js 版本的基礎鏡像
FROM nikolaik/python-nodejs:python3.11-nodejs22

# 接收 n8n 版本號作為構建參數
ARG N8N_VERSION

# 檢查 N8N_VERSION 是否已提供，如果沒有則構建失敗
RUN if [ -z "$N8N_VERSION" ] ; then echo "The N8N_VERSION argument is missing!" ; exit 1; fi

# --- 修正點 1: 合併多個 RUN 指令，並移除錯誤的使用者修改命令 ---
# 原有的 usermod 和 groupmod 命令是錯誤的，因為基礎鏡像中不存在名為 'pn' 的使用者，
# 內建的使用者就是 'node'，所以無需修改。
RUN \
	apt-get update && \
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

# 安裝 Python 依賴
COPY requirements.txt /requirements/requirements.txt
RUN \
	python -m pip install --upgrade pip setuptools wheel && \
	pip install -r /requirements/requirements.txt

# 切換到 root 使用者以進行全域安裝
USER root

# 安裝 n8n 和社群節點
# --- 修正點 2: 將多個 npm install 合併到一個 RUN 層中以優化 ---
RUN \
	npm_config_user=root npm install -g full-icu n8n@${N8N_VERSION} && \
	cd /usr/local/lib/node_modules/n8n && \
	npm install n8n-nodes-python n8n-nodes-changed n8n-nodes-updates n8n-nodes-influxdb n8n-nodes-tavily

# --- 修正點 3: 修正中文化包的版本匹配邏輯並增加錯誤處理 ---
# 嘗試下載與 n8n 版本完全對應的中文化包，如果找不到則跳過，避免構建失敗。
RUN \
    PURE_N8N_VERSION=${N8N_VERSION#n8n@} && \
    echo "正在為 n8n 版本 ${PURE_N8N_VERSION} 嘗試下載對應的中文化包..." && \
    ( \
        mkdir -p /tmp/n8n-i18n && \
        curl -fL -o /tmp/n8n-i18n/editor-ui.tar.gz "https://github.com/other-blowsnow/n8n-i18n-chinese/releases/download/n8n%40${PURE_N8N_VERSION}/editor-ui.tar.gz" && \
        echo "中文化包下載成功，正在安裝..." && \
        mkdir -p /usr/local/lib/node_modules/n8n/node_modules/n8n-editor-ui/dist && \
        tar -xzf /tmp/n8n-i18n/editor-ui.tar.gz -C /usr/local/lib/node_modules/n8n/node_modules/n8n-editor-ui/dist \
    ) || echo "警告：未找到版本 ${PURE_N8N_VERSION} 對應的中文化包，將使用預設英文介面。" && \
    rm -rf /tmp/n8n-i18n

# 設定環境變數 (修正為 key=value 格式)
ENV N8N_DEFAULT_LOCALE=zh-CN
ENV PYTHONPATH=/usr/local/lib/python3.11/dist-packages:/home/pyodide/lib/python3.11/site-packages
ENV NODE_ICU_DATA=/usr/local/lib/node_modules/full-icu

# 新增：確保 node 使用者和群組存在，然後設定權限
RUN \
    (getent group node >/dev/null || addgroup --gid 1000 node) && \
    (getent passwd node >/dev/null || adduser --uid 1000 --gid 1000 --disabled-password --gecos "" --home /home/node node) && \
    chown -R node:node /home/node

# 設定工作目錄
WORKDIR /data

# 複製並設定入口點腳本
COPY docker-entrypoint.sh /docker-entrypoint.sh
RUN chmod +x /docker-entrypoint.sh
ENTRYPOINT ["/docker-entrypoint.sh"]

# 開放 n8n 預設端口
EXPOSE 5678/tcp
