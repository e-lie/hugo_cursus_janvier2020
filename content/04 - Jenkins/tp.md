

# https://jenkins-x.io/docs/getting-started/setup/install/

- Download last version : `curl -L "https://github.com/jenkins-x/jx/releases/download/$(curl --silent "https://github.com/jenkins-x/jx/releases/latest" | sed 's#.*tag/\(.*\)\".*#\1#')/jx-linux-amd64.tar.gz" -o /tmp/jx.tar.gz`
- Extract archive: `tar -xzvf /tmp/jx.tar.gz -C /tmp/`
- Move to /usr/bin: `sudo mv /tmp/jx /usr/bin/`
- Test installation: `jx --version`

# 