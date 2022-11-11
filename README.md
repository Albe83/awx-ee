## Building environment
```bash
apt install docker.io
apt install python3-pip
pip install ansible-builder
pip install "https://releases.ansible.com/ansible-tower/cli/ansible-tower-cli-latest.tar.gz"

export DOCKER_HUB_USERNAME="albe83"
export DOCKER_HUB_PASSWORD="xxxx"
docker login --username="$DOCKER_HUB_USERNAME" --password="$DOCKER_HUB_PASSWORD"

export TOWER_HOST="https://awx.example.org"
export TOWER_USERNAME="admin"
export TOWER_PASSWORD="zzz"
export TOWER_OAUTH_TOKEN=$(awx login -f human)

```

## Prepare for building
```bash
git clone "https://github.com/Albe83/awx-ee.git"
cd awx-ee

```

## Set image name
```bash
export EE_IMAGE_NAME="custom-awx-ee"
export EE_IMAGE_VERSION_MAJOR="0"
export EE_IMAGE_VERSION_MINOR="0"
export EE_IMAGE_VERSION_PATCH="1"

export EE_IMAGE_NAME_MAJOR="$EE_IMAGE_NAME:v$EE_IMAGE_VERSION_MAJOR"
export EE_IMAGE_NAME_MINOR="$EE_IMAGE_NAME_MAJOR.$EE_IMAGE_VERSION_MINOR"
export EE_IMAGE_NAME_PATCH="$EE_IMAGE_NAME_MINOR.$EE_IMAGE_VERSION_PATCH"

export EE_IMAGE_FULLNAME="$DOCKER_HUB_USERNAME/$EE_IMAGE_NAME"
export EE_IMAGE_FULLNAME_MAJOR="$DOCKER_HUB_USERNAME/$EE_IMAGE_NAME_MAJOR"
export EE_IMAGE_FULLNAME_MINOR="$DOCKER_HUB_USERNAME/$EE_IMAGE_NAME_MINOR"
export EE_IMAGE_FULLNAME_PATCH="$DOCKER_HUB_USERNAME/$EE_IMAGE_NAME_PATCH"


export EE_FULL_NAME="$EE_IMAGE_NAME:v$EE_IMAGE_VERSION_MAJOR.$EE_IMAGE_VERSION_MINOR.$EE_IMAGE_VERSION_PATCH"

```
## Building
```bash
ansible-builder build \
  --container-runtime docker \
  --prune-images \
  --tag "$EE_IMAGE_FULLNAME_MAJOR" \
  --tag "$EE_IMAGE_FULLNAME_MINOR" \
  --tag "$EE_IMAGE_FULLNAME_PATCH"

```

## Publishing
```bash
docker push --all-tags "$EE_IMAGE_FULLNAME"

```

## Cleaning up
```bash
cd ..
docker rmi "$EE_IMAGE_FULLNAME_MAJOR" "$EE_IMAGE_FULLNAME_MINOR" "$EE_IMAGE_FULLNAME_PATCH"
rm -fR awx-ee

```
