## Building environment
```bash
apt install docker.io
apt install python3-pip
pip install ansible-builder
```

## Prepare for building
```bash
git clone "https://github.com/Albe83/awx-ee.git"
cd awx-ee
```

## Set image name
```bash
export EE_IMAGE_NAME="albe83/custom-awx-ee"
export EE_IMAGE_VERSION_MAJOR="0"
export EE_IMAGE_VERSION_MINOR="0"
export EE_IMAGE_VERSION_PATCH="1"
export EE_FULL_NAME="$EE_IMAGE_NAME:v$EE_IMAGE_VERSION_MAJOR.$EE_IMAGE_VERSION_MINOR.$EE_IMAGE_VERSION_PATCH"
```
## Building
```bash
ansible-builder build --container-runtime docker --prune-images --tag "$EE_FULL_NAME"
docker tag "$EE_FULL_NAME" "$EE_IMAGE_NAME:v$EE_IMAGE_VERSION_MAJOR.$EE_IMAGE_VERSION_MINOR" "$EE_IMAGE_NAME:v$EE_IMAGE_VERSION_MAJOR"
```

## Publishing
```bash
docker push --all-tags "$EE_FULL_NAME"
```

## Cleaning up
```bash
docker rmi "$EE_FULL_NAME" "$EE_IMAGE_NAME:v$EE_IMAGE_VERSION_MAJOR.$EE_IMAGE_VERSION_MINOR" "$EE_IMAGE_NAME:v$EE_IMAGE_VERSION_MAJOR"
```
