FROM registry.renkulab.io/rok.roskar/vscode-example:0c10599 as code-server
FROM renku/renkulab-r:4.3.1-0.23.0 AS rstudio-server
FROM renku/renkulab-py:3.10-0.23.0 as jupyterlab
FROM code-server

COPY environment.yml* .devcontainer/noop.txt /tmp/conda-tmp/
COPY --chmod=755 .devcontainer/entrypoint.sh /entrypoint.sh

USER root

RUN curl -sSL https://github.com/prefix-dev/pixi/releases/download/v0.35.0/pixi-x86_64-unknown-linux-musl.tar.gz -o pixi.tgz && \
    tar -xvzf pixi.tgz && \
    mv pixi /usr/local/bin && \
    pixi config set detached-environments $HOME/work/.envs && \
    rm pixi.tgz

USER jovyan

RUN if [ -f "/tmp/conda-tmp/environment.yml" ]; then umask 0002 && \
    /opt/conda/bin/mamba env create -n project -f /tmp/conda-tmp/environment.yml; fi && \
    mamba init && \
    mamba install -y -n project ipykernel && \
    /opt/conda/envs/project/bin/python -m ipykernel install --name project --display-name "Project environment" --user && \
    echo "conda activate /home/jovyan/work/.envs/project" >> $HOME/.bashrc
