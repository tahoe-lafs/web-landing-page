# Pull base image from official repo
FROM rubylang/ruby:3.3.0-jammy

# Install some required packages for building Gems
RUN INSTALL_PKGS="gcc g++ libyaml-dev make nodejs" && \
    apt-get -q clean && \
    apt-get -q update && \
    apt-get install -y $INSTALL_PKGS && \
    apt-get -q clean

# Parameters for default user:group
ARG uid=1000
ARG user=ubuntu
ARG gid=1000
ARG group=ubuntu

# Add or modify user and group for build and runtime (convenient)
#RUN id ${user} > /dev/null 2>&1 && \
#    { groupmod -g "${gid}" "${group}" && usermod -md /home/${user} -s /bin/bash -g "${group}" -u "${uid}" "${user}"; } || \
#    { groupadd -g "${gid}" "${group}" && useradd -md /home/${user} -s /bin/bash -g "${group}" -u "${uid}" "${user}"; }

# Copy requirements in non-root user home directory
COPY Gemfile Gemfile.lock "/home/${user}/"
RUN chown "${user}:${group}" "/home/${user}/Gemfile"*

# Switch to non-root user
USER ${user}
WORKDIR /home/${user}

# Install required gems
RUN echo "gem: --no-document --user-install --bindir /home/${user}/bin" >> /home/${user}/.gemrc && \
    echo "gempath: /home/${user}/.gem/ruby:/home/${user}/.bundle/gems/ruby/3.3.0" >> .gemrc && \
    gem install bundler --version `sed -n -r -e '/BUNDLED WITH/,$ { s/\s+([.0-9]+)/\1/ p }' Gemfile.lock` && \
    bundle config --global path /home/${user}/.bundle/gems && \
    bundle config --global bin /home/${user}/bin && \
    bundle install && \
    rm -rf /home/${user}/.bundle/cache

ENV PATH=/home/${user}/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

CMD [ 'jekyll', 's', 'source', './src', '--verbose', '--host 0.0.0.0', '--incremental' ]
