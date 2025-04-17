#python更新至3.11，nodejs版本不能大于22
FROM nikolaik/python-nodejs:python3.11-nodejs22

# changing user `pn` to `node`
RUN usermod --login node --move-home --home /home/node pn
RUN groupmod --new-name node pn

ARG N8N_VERSION

RUN if [ -z "$N8N_VERSION" ] ; then echo "The N8N_VERSION argument is missing!" ; exit 1; fi

RUN \
	apt-get update && \
	apt-get -y install graphicsmagick gosu git

RUN apt-get update && apt-get install -y \
    python3-dev \
    gcc \
    g++ \
    #linux-headers-$(uname -r) \
    build-essential && \
    apt-get clean && rm -rf /var/lib/apt/lists/*
    
# Install python requirements
RUN mkdir /requirements
COPY requirements.txt /requirements/requirements.txt
RUN python -m pip install --upgrade pip setuptools wheel
RUN pip install -r /requirements/requirements.txt

# Set a custom user to not have n8n run as root
USER root

RUN npm_config_user=root npm install -g full-icu n8n@${N8N_VERSION}

# Install n8n-nodes-python module
#RUN cd /usr/lib/node_modules/n8n && npm install n8n-nodes-python
RUN cd /usr/local/lib/node_modules/n8n && npm install n8n-nodes-python
RUN cd /usr/local/lib/node_modules/n8n && npm install n8n-nodes-changed
RUN cd /usr/local/lib/node_modules/n8n && npm install n8n-nodes-updates
RUN cd /usr/local/lib/node_modules/n8n && npm install n8n-nodes-influxdb
RUN cd /usr/local/lib/node_modules/n8n && npm install n8n-nodes-tavily



# 引入 n8n-i18n-chinese 项目，配置 n8n 中文支持
#RUN apt-get update && apt-get install -y curl unzip && \
    #mkdir -p /tmp/n8n-i18n && \
    #LATEST_VERSION_i18n=$(curl -s https://api.github.com/repos/n8n-io/n8n/releases/latest | jq -r '.tag_name')
    #curl -L -o /tmp/n8n-i18n/master.zip https://github.com/other-blowsnow/n8n-i18n-chinese/archive/refs/heads/master.zip && \
    #unzip /tmp/n8n-i18n/master.zip -d /tmp/n8n-i18n && \
    #cp -r /tmp/n8n-i18n/n8n-i18n-chinese-master/locales /usr/local/lib/node_modules/n8n/ && \
    #cp -r /tmp/n8n-i18n/n8n-i18n-chinese-master/dist/* /usr/local/lib/node_modules/n8n/dist/ && \
    #rm -rf /tmp/n8n-i18n && \
    #apt-get clean && rm -rf /var/lib/apt/lists/*
RUN cd /usr/local/lib/node_modules/n8n && npm install n8n-nodes-python

# 引入 n8n-i18n-chinese 项目，配置 n8n 中文支持
RUN apt-get update && apt-get install -y curl tar gzip && \
    mkdir -p /tmp/n8n-i18n && \
    curl -L -o /tmp/n8n-i18n/editor-ui.tar.gz "https://github.com/other-blowsnow/n8n-i18n-chinese/releases/download/n8n%401.88.0/editor-ui.tar.gz" && \
    mkdir -p /usr/local/lib/node_modules/n8n/node_modules/n8n-editor-ui/dist && \
    tar -xzf /tmp/n8n-i18n/editor-ui.tar.gz -C /usr/local/lib/node_modules/n8n/node_modules/n8n-editor-ui/dist && \
    rm -rf /tmp/n8n-i18n && \
    apt-get clean && rm -rf /var/lib/apt/lists/*





# 设置 n8n 默认语言为中文
ENV N8N_DEFAULT_LOCALE=zh-CN

# 设置 PYTHONPATH 环境变量，确保 Python 能找到所有已安装的包
ENV PYTHONPATH=/usr/local/lib/python3.11/dist-packages:/home/pyodide/lib/python3.11/site-packages
ENV NODE_ICU_DATA /usr/lib/node_modules/full-icu

WORKDIR /data

COPY docker-entrypoint.sh /docker-entrypoint.sh
ENTRYPOINT ["/docker-entrypoint.sh"]

EXPOSE 5678/tcp
