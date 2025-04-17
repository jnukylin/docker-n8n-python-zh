#更新为python3.11 
FROM nikolaik/python-nodejs:python3.11-nodejs22-alpine

# changing user `pn` to `node`
RUN deluser pn && rm -r /home/pn # delete: user + group
RUN addgroup -g 1000 -S node && adduser -u 1000 -S node -G node -s /bin/sh # create: user + group

ARG N8N_VERSION

RUN if [ -z "$N8N_VERSION" ] ; then echo "The N8N_VERSION argument is missing!" ; exit 1; fi

# Update everything and install needed dependencies
RUN apk add --update graphicsmagick tzdata git tini su-exec

RUN apk add --no-cache \
    python3-dev \
    py3-pip \
    gcc \
    g++ \
    musl-dev \
    linux-headers \
    build-base
    
# # Set a custom user to not have n8n run as root
USER root

# Install n8n and the also temporary all the packages
# it needs to build it correctly.
RUN apk --update add --virtual build-dependencies build-base ca-certificates && \
	npm_config_user=root npm install -g full-icu n8n@${N8N_VERSION} && \
	apk del build-dependencies \
	&& rm -rf /root /tmp/* /var/cache/apk/* && mkdir /root;

# Install n8n-nodes-python module
RUN cd /usr/local/lib/node_modules/n8n && npm install n8n-nodes-python

# 引入 n8n-i18n-chinese 项目，配置 n8n 中文支持
#RUN apt-get update && apt-get install -y curl unzip && \
    #mkdir -p /tmp/n8n-i18n && \
    #curl -L -o /tmp/n8n-i18n/master.zip https://github.com/other-blowsnow/n8n-i18n-chinese/archive/refs/heads/master.zip && \
    #unzip /tmp/n8n-i18n/master.zip -d /tmp/n8n-i18n && \
    #cp -r /tmp/n8n-i18n/n8n-i18n-chinese-master/locales /usr/local/lib/node_modules/n8n/ && \
    #cp -r /tmp/n8n-i18n/n8n-i18n-chinese-master/dist/* /usr/local/lib/node_modules/n8n/dist/ && \
    #rm -rf /tmp/n8n-i18n && \
    #apt-get clean && rm -rf /var/lib/apt/lists/*

# 设置 n8n 默认语言为中文
#ENV N8N_DEFAULT_LOCALE=zh-CN

# 引入 n8n-i18n-chinese 项目，配置 n8n 中文支持
#RUN apk add --no-cache curl tar gzip && \
    #mkdir -p /tmp/n8n-i18n && \
    #curl -L -o /tmp/n8n-i18n/editor-ui.tar.gz "https://github.com/other-blowsnow/n8n-i18n-chinese/releases/download/n8n%401.88.0/editor-ui.tar.gz" && \
    #mkdir -p /usr/local/lib/node_modules/n8n/node_modules/n8n-editor-ui/dist && \
    #tar -xzf /tmp/n8n-i18n/editor-ui.tar.gz -C /usr/local/lib/node_modules/n8n/node_modules/n8n-editor-ui/dist && \
    #rm -rf /tmp/n8n-i18n
# 引入 n8n-i18n-chinese 项目，配置 n8n 中文支持
RUN apk add --no-cache curl tar gzip jq && \
    # 获取 n8n 最新版本号
    LATEST_VERSION_i18n=$(curl -s https://api.github.com/repos/other-blowsnow/n8n-i18n-chinese/releases/latest | jq -r '.tag_name') && \
    echo "最新版本: ${LATEST_VERSION_i18n}" && \
    mkdir -p /tmp/n8n-i18n && \
    # 使用获取的版本号构建下载链接
    curl -L -o /tmp/n8n-i18n/editor-ui.tar.gz "https://github.com/other-blowsnow/n8n-i18n-chinese/releases/download/${LATEST_VERSION_i18n}/editor-ui.tar.gz" && \
    mkdir -p /usr/local/lib/node_modules/n8n/node_modules/n8n-editor-ui/dist && \
    tar -xzf /tmp/n8n-i18n/editor-ui.tar.gz -C /usr/local/lib/node_modules/n8n/node_modules/n8n-editor-ui/dist && \
    rm -rf /tmp/n8n-i18n


# 设置 n8n 默认语言为中文
ENV N8N_DEFAULT_LOCALE=zh-CN

# 设置 PYTHONPATH 环境变量，确保 Python 能找到所有已安装的包
ENV PYTHONPATH=/usr/local/lib/python3.11/dist-packages:/home/pyodide/lib/python3.11/site-packages
ENV NODE_ICU_DATA /usr/lib/node_modules/full-icu

# Install fonts
RUN apk --no-cache add --virtual fonts msttcorefonts-installer fontconfig && \
	update-ms-fonts && \
	fc-cache -f && \
	apk del fonts && \
	find  /usr/share/fonts/truetype/msttcorefonts/ -type l -exec unlink {} \; \
	&& rm -rf /root /tmp/* /var/cache/apk/* && mkdir /root

# (note: use debian instead of alpine if you want to use Pandas or heavy libraries)
# Install Python Development Tools (disabled)
# RUN apk update && apk --no-cache add make automake gcc g++ subversion python3-dev
# RUN apk update && apk --no-cache add musl-dev linux-headers g++
# RUN echo "http://dl-cdn.alpinelinux.org/alpine/edge/community" >> /etc/apk/repositories
# RUN apk --no-cache --update-cache add gfortran build-base wget freetype-dev libpng-dev openblas-dev
# RUN apk --no-cache --update-cache add wget libgfortran musl-dev linux-headers
RUN pip install --no-cache-dir markitdown
# Install python requirements
RUN mkdir /requirements
COPY requirements.txt /requirements/requirements.txt
RUN python -m pip install --upgrade pip setuptools wheel
#RUN pip install -r /requirements/requirements.txt
RUN pip install --no-cache-dir -r /requirements/requirements.txt
ENV NODE_ICU_DATA /usr/local/lib/node_modules/full-icu

WORKDIR /data

COPY docker-entrypoint.sh /docker-entrypoint.sh
ENTRYPOINT ["tini", "--", "/docker-entrypoint.sh"]

EXPOSE 5678/tcp
