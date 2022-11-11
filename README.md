## Building environment
```bash
apt install docker.io
apt install python3-pip
pip install ansible-builder
pip install "https://releases.ansible.com/ansible-tower/cli/ansible-tower-cli-latest.tar.gz"

```

## Configure for building
```bash
export GIT_REPO_AWX_EE="https://github.com/Albe83/awx-ee.git"

export DOCKER_HUB_USERNAME="albe83"
export DOCKER_HUB_PASSWORD="xxxx"

export TOWER_HOST="http://127.0.0.1:30080"
export TOWER_USERNAME="admin"
export TOWER_PASSWORD="zzz"

export EE_IMAGE_NAME="custom-awx-ee"
export EE_IMAGE_VERSION_MAJOR="0"
export EE_IMAGE_VERSION_MINOR="0"
export EE_IMAGE_VERSION_PATCH="1"

```

## Prepare for building
```bash
git clone "$GIT_REPO_AWX_EE"
cd awx-ee

```

## Set image name
```bash
export EE_IMAGE_NAME_MAJOR="$EE_IMAGE_NAME:v$EE_IMAGE_VERSION_MAJOR"
export EE_IMAGE_NAME_MINOR="$EE_IMAGE_NAME_MAJOR.$EE_IMAGE_VERSION_MINOR"
export EE_IMAGE_NAME_PATCH="$EE_IMAGE_NAME_MINOR.$EE_IMAGE_VERSION_PATCH"

export EE_IMAGE_FULLNAME="$DOCKER_HUB_USERNAME/$EE_IMAGE_NAME"
export EE_IMAGE_FULLNAME_MAJOR="$DOCKER_HUB_USERNAME/$EE_IMAGE_NAME_MAJOR"
export EE_IMAGE_FULLNAME_MINOR="$DOCKER_HUB_USERNAME/$EE_IMAGE_NAME_MINOR"
export EE_IMAGE_FULLNAME_PATCH="$DOCKER_HUB_USERNAME/$EE_IMAGE_NAME_PATCH"

ansible-builder build \
  --container-runtime docker \
  --prune-images \
  --tag "$EE_IMAGE_FULLNAME_MAJOR" \
  --tag "$EE_IMAGE_FULLNAME_MINOR" \
  --tag "$EE_IMAGE_FULLNAME_PATCH"

```

## Publishing
```bash
docker login --username="$DOCKER_HUB_USERNAME" --password="$DOCKER_HUB_PASSWORD"
docker push --all-tags "$EE_IMAGE_FULLNAME"
docker logout

```

## Cleaning up
```bash
cd ..
docker rmi "$EE_IMAGE_FULLNAME_MAJOR" "$EE_IMAGE_FULLNAME_MINOR" "$EE_IMAGE_FULLNAME_PATCH"
rm -fR awx-ee

```

# Configure AWX
```bash
export AWX_LOGIN_TOKEN_DESC="AWX CLI - $(date "+%Y%m%d%H%M%S%N")"
$(awx login -f human --description "$AWX_LOGIN_TOKEN_DESC")

export AWX_EE_NAME="${EE_IMAGE_NAME^^}"
export AWX_EE_NAME_MAJOR="$AWX_EE_NAME (v$EE_IMAGE_VERSION_MAJOR)"
export AWX_EE_NAME_MINOR="$AWX_EE_NAME (v$EE_IMAGE_VERSION_MAJOR.$EE_IMAGE_VERSION_MINOR)"
export AWX_EE_NAME_PATCH="$AWX_EE_NAME (v$EE_IMAGE_VERSION_MAJOR.$EE_IMAGE_VERSION_MINOR.$EE_IMAGE_VERSION_PATCH)"

awx execution_environments delete "$AWX_EE_NAME_MAJOR"
awx execution_environments delete "$AWX_EE_NAME_MINOR"
awx execution_environments delete "$AWX_EE_NAME_PATCH"

awx execution_environments create --name "$AWX_EE_NAME_MAJOR" --image "$EE_IMAGE_FULLNAME_MAJOR" --pull "always"
awx execution_environments create --name "$AWX_EE_NAME_MINOR" --image "$EE_IMAGE_FULLNAME_MINOR" --pull "always"
awx execution_environments create --name "$AWX_EE_NAME_PATCH" --image "$EE_IMAGE_FULLNAME_PATCH" --pull "always"

```