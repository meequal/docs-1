# Building from sources

You may want to build the library from sources to learn internal details or/and to contribute to the project.

## Build library

Install prerequisites \(all platforms\):

```text
npm install -g yarn
npm install -g lerna
```

Install prerequisites \(as root\) \(Debian/Ubuntu Linux\):

```text
sudo apt-get install build-essential libudev-dev
```

Build project from sources:

```text
# clone repository
git clone https://github.com/bZxNetwork/bZx-monorepo.git
# change working folder
cd bZx-monorepo/
# install dependencies for all projects
lerna bootstrap
# build all projects in monorepo
yarn run build-all
```

