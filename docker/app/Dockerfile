FROM goferex/docker-cli-base:debian AS tools-builder

FROM debian:latest

RUN apt update -y && apt upgrade -y \
 && apt install -y gpg procps sysstat unzip zip

COPY --from=tools-builder /artifacts.tar.gz /
RUN tar xf /artifacts.tar.gz -C / \
 && rm /artifacts.tar.gz

ARG locale='ja_JP.UTF-8'
ENV LANG=${locale}
ENV LC_ALL=${locale}

ARG tz='Asia/Tokyo'
ENV TZ=${tz}

ARG shell='zsh'
ENV SHELL=${shell}

ARG term='xterm-256color'
ENV TERM=${term}

RUN apt install -y locales tzdata \
 && locale-gen ${locale} \
 && localedef -i $(echo $LANG | cut -d . -f 1) -f $(echo $LANG | cut -d . -f 2) ${locale} \
 && cp -f /usr/share/zoneinfo/Asia/Tokyo /etc/localtime \
 && echo ${tz} > /etc/timezone

ARG username='user'
ARG password='password'

RUN apt install -y ${shell} \
 && groupadd --gid 1000 ${username} \
 && echo `which ${shell}` \
 && useradd --uid 1000 --create-home --gid ${username} --shell $(which ${shell}) ${username} \
 && apt install -y sudo \
 && echo "${username} ALL=(ALL) ALL" > /etc/sudoers.d/${username} \
 && echo "${username}:${password}" | chpasswd

RUN apt install -y awscli build-essential curl fonts-noto fontconfig-config git jq libpq-dev neovim rbenv ruby-dev tmux

USER ${username}

ARG ruby_version=3.3.1
ENV NVM_DIR=/home/${username}/.nvm
ENV GEM_HOME=/home/${username}/.gem
RUN rbenv install ${ruby_version} \
 && rbenv exec gem install bundler \
 && curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/master/install.sh | bash \
 && bash -c "source /home/${username}/.nvm/nvm.sh && nvm install --lts && nvm use --lts"

COPY --chown="${username}:${username}" --chmod=0644 zshrc    /home/${username}/.zshrc
COPY --chown="${username}:${username}" --chmod=0644 zshkbd   /home/${username}/.zshkbd
COPY --chown="${username}:${username}" --chmod=0644 zshenv   /home/${username}/.zshenv
COPY --chown="${username}:${username}" --chmod=0644 p10k.zsh /home/${username}/.p10k.zsh

ARG git_user_name='Your Name'
ARG git_user_email='you@example.com'

RUN git config --global init.defaultBranch main \
 && git config --global sequence.editor interactive-rebase-tool \
 && git config --global user.email "$git_user_name" \
 && git config --global user.name "$git_user_email"

WORKDIR /home/${username}/workspaces
