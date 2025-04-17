# 我将n8n、python、中文包组合在一起，参照项目有[naskio/docker-n8n-python](https://github.com/naskio/docker-n8n-python)、[other-blowsnow/n8n-i18n-chinese](https://github.com/other-blowsnow/n8n-i18n-chinese)等

# n8n - docker image with Python 3.11 and custom node PythonFunction


![n8n.io - Workflow Automation](https://raw.githubusercontent.com/n8n-io/n8n/master/assets/n8n-logo.png)
<svg height="1em" style="flex:none;line-height:1" viewBox="0 0 24 24" width="1em" xmlns="http://www.w3.org/2000/svg"><title>n8n</title><path clip-rule="evenodd" d="M24 8.4c0 1.325-1.102 2.4-2.462 2.4-1.146 0-2.11-.765-2.384-1.8h-3.436c-.602 0-1.115.424-1.214 1.003l-.101.592a2.38 2.38 0 01-.8 1.405c.412.354.704.844.8 1.405l.1.592A1.222 1.222 0 0015.719 15h.975c.273-1.035 1.237-1.8 2.384-1.8 1.36 0 2.461 1.075 2.461 2.4S20.436 18 19.078 18c-1.147 0-2.11-.765-2.384-1.8h-.975c-1.204 0-2.23-.848-2.428-2.005l-.101-.592a1.222 1.222 0 00-1.214-1.003H10.97c-.308.984-1.246 1.7-2.356 1.7-1.11 0-2.048-.716-2.355-1.7H4.817c-.308.984-1.246 1.7-2.355 1.7C1.102 14.3 0 13.225 0 11.9s1.102-2.4 2.462-2.4c1.183 0 2.172.815 2.408 1.9h1.337c.236-1.085 1.225-1.9 2.408-1.9 1.184 0 2.172.815 2.408 1.9h.952c.601 0 1.115-.424 1.213-1.003l.102-.592c.198-1.157 1.225-2.005 2.428-2.005h3.436c.274-1.035 1.238-1.8 2.384-1.8C22.898 6 24 7.075 24 8.4zm-1.23 0c0 .663-.552 1.2-1.232 1.2-.68 0-1.23-.537-1.23-1.2 0-.663.55-1.2 1.23-1.2.68 0 1.231.537 1.231 1.2zM2.461 13.1c.68 0 1.23-.537 1.23-1.2 0-.663-.55-1.2-1.23-1.2-.68 0-1.231.537-1.231 1.2 0 .663.55 1.2 1.23 1.2zm6.153 0c.68 0 1.231-.537 1.231-1.2 0-.663-.55-1.2-1.23-1.2-.68 0-1.231.537-1.231 1.2 0 .663.55 1.2 1.23 1.2zm10.462 3.7c.68 0 1.23-.537 1.23-1.2 0-.663-.55-1.2-1.23-1.2-.68 0-1.23.537-1.23 1.2 0 .663.55 1.2 1.23 1.2z" fill="#EA4B71" fill-rule="evenodd"></path></svg>

This [image](https://hub.docker.com/r/naskio/n8n-python) includes Python 3.11 by default. it can be used to run python
scripts inside n8n using the [Execute Command](https://docs.n8n.io/nodes/n8n-nodes-base.executeCommand/) node or code
snippets using the custom node [Python Function](https://www.github.com/naskio/n8n-nodes-python).

> Run python 3.11 code on n8n.

[Docker Hub](https://hub.docker.com/r/jnukylin/n8n-python-zh)

[GitHub repository](https://www.github.com/jnukylin/docker-n8n-python-zh)

## Image Setup

Using docker compose
[docker-compose.yml](./demo/docker-compose-local.yml)

## Usage

### Run Python Code

The image includes the custom module [n8n-nodes-python](https://www.github.com/jnukylin/n8n-nodes-python-zh) by default.

We can use this custom node [Python Function](https://www.github.com/jnukylin/n8n-nodes-python-zh) to run a python code over
the `items` (works the same way as [Function](https://docs.n8n.io/nodes/n8n-nodes-base.function) node)

[Python Function node docs](https://www.github.com/jnukylin/n8n-nodes-python-zh)

### Run mounted Python scripts using the ExecuteCommand node

You can run `*.py`files that has been mounted to the container using
the [ExecuteCommand](https://docs.n8n.io/nodes/n8n-nodes-base.executeCommand/) node.

### Installing external packages

The [ExecuteCommand](https://docs.n8n.io/nodes/n8n-nodes-base.executeCommand/) node can be used to install python
packages or install dependencies from a mounted `requirements.txt` file to the container.

It can be combined with [n8nTrigger](https://docs.n8n.io/nodes/n8n-nodes-base.n8nTrigger) to install packages directly
after starting the container.

> Once the packages are installed, it can be used in the Python Function node. `import <package>`

## Documentation

The official n8n documentation can be found under: [https://docs.n8n.io](https://docs.n8n.io)

Additional information and example workflows on the n8n.io website: [https://n8n.io](https://n8n.io)

Learn [how to run n8n in **Docker**](https://github.com/n8n-io/n8n/tree/master/docker/images/n8n/README.md)
