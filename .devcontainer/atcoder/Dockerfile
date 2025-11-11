FROM gcc:15.2.0-bookworm

ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID
ARG LIBRARY_PATH=/workspace/competitive/library
ARG NVM_VERSION=v0.40.3

##
# create vscode user
##
RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
    #
    # add sudo support
    && apt-get update \
    && apt-get install -y --no-install-recommends sudo \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME \
    #
    # clean up
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# set the default user
USER $USERNAME

##
# pre-compile bits/stdc++.h
##
USER root
RUN cd /usr/local/include/c++/15.2.0/x86_64-linux-gnu/bits/ \
    && mkdir stdc++.h.gch \
    && g++ -std=gnu++23 stdc++.h -o stdc++.h.gch/debug.gch -g -O0 -I $LIBRARY_PATH/* -DDEBUG -D_FORTIFY_SOURCE=2 -D_GLIBCXX_DEBUG -D_GLIBCXX_DEBUG_PEDANTIC -fdiagnostics-color=always -fno-sanitize-recover -fsanitize=undefined,address -Wall -Wextra -Wshadow -Wformat=2 -Wfloat-equal -Wconversion -Wlogical-op -Wshift-overflow=2 -Wduplicated-cond -Wcast-qual -Wcast-align=strict \
    && g++ -std=gnu++23 stdc++.h -o stdc++.h.gch/normal.gch -O2 -I $LIBRARY_PATH/*
USER $USERNAME

##
# install gdb
##
USER root
RUN apt-get update \
    && apt-get install -y --no-install-recommends gdb \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*
USER $USERNAME

##
# install online-judge-tools
##
USER root
RUN apt-get update \
    && apt-get install -y --no-install-recommends pipx \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*
USER $USERNAME
RUN pipx ensurepath
RUN pipx install online-judge-tools
RUN pipx install online-judge-verify-helper
RUN pipx install online-judge-template-generator

##
# install atcoder-cli
##
SHELL ["/bin/bash", "-c"]
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/$NVM_VERSION/install.sh | bash
RUN . $HOME/.nvm/nvm.sh \
    && nvm install node \
    && npm install -g npm \
    && npm install -g atcoder-cli

##
# install other packages
##
USER root
RUN apt-get update \
    && apt-get install -y --no-install-recommends build-essential python3-dev \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*
USER $USERNAME
