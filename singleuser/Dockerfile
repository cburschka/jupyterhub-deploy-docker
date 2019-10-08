ARG DOCKER_NOTEBOOK_IMAGE

FROM cburschka/ihaskell AS ihaskell

RUN ln -s $HOME/ihaskell $HOME/.stack/global-project && \
    ln -s $(stack path --local-install-root) $HOME/.stack/.local-install-root && \
    ln -s $(stack path --snapshot-install-root) $HOME/.stack/.snapshot-install-root && \
    ln -s $(stack path --compiler-bin) $HOME/.stack/.compiler-bin

FROM $DOCKER_NOTEBOOK_IMAGE
ARG JUPYTERHUB_VERSION
RUN python3 -m pip install --upgrade pip
RUN python3 -m pip install --no-cache jupyterhub==$JUPYTERHUB_VERSION

USER root
COPY --from=ihaskell /opt/ghc /opt/ghc
COPY --from=ihaskell --chown=jovyan:users /home/jovyan/.stack /home/jovyan/.stack
COPY --from=ihaskell --chown=jovyan:users /home/jovyan/ihaskell /home/jovyan/ihaskell
COPY --from=ihaskell --chown=jovyan:users /home/jovyan/.local /home/jovyan/.local
COPY --from=ihaskell /usr/local/bin /usr/local/bin
COPY --from=ihaskell /usr/lib/x86_64-linux-gnu /usr/lib/x86_64-linux-gnu
#COPY --from=$DOCKER_NOTEBOOK_IMAGE /usr/local/bin /usr/local/bin

# these appear outdated and redundant.
#RUN git clone https://github.com/cburschka/IHaskell && \
#    cd IHaskell && \
#    pip install -r requirements.txt

RUN /home/jovyan/.local/bin/ihaskell install --stack --prefix=/opt/conda
RUN chown -R jovyan:users /home/jovyan/.ihaskell

USER jovyan

ENV PATH $HOME/.local/bin:$HOME/.stack/.local-install-root/bin:$HOME/.stack/.snapshot-install-root/bin:$HOME/.stack/.compiler-bin:${PATH}
