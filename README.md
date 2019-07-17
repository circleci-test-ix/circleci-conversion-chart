# TravisCI to CircleCI Conversion Chart

#### Cache a folder

Travis

```yaml
cache:  
  - directories:
    - node_modules
```

Circle

```yaml
- save_cache:
    key: node_modules-{{ checksum "package.json" }}
    paths:
      - node_modules

- restore_cache:
    key: node_modules-{{ checksum "package.json" }}
```

#### Use xenial

Travis

```yaml
dist: xenial
```

Circle

```yaml
docker:
  - image: buildpack-deps:xenial
```

#### Use clang compiler

Travis

```yaml
compiler: clang
```

Circle

```yaml
- run:
    name: Install Clang
    command: |
      apt-get update
      apt install -y software-properties-common
      wget -O - https://apt.llvm.org/llvm-snapshot.gpg.key | apt-key add -        
      apt-add-repository "deb http://apt.llvm.org/xenial/ llvm-toolchain-xenial main"
      apt-get update
      apt-get install -y clang
      # Set compiler to clang 
      echo 'export CC=${CC:-clang}' >> $BASH_ENV
      echo 'export CC_FOR_BUILD=${CC_FOR_BUILD:-clang}' >> $BASH_ENV
```

#### Use specific python version

Travis

```yaml
language: python      
python: 3.6
```

Circle

```yaml
docker:
  - image: circleci/python:3.6
```

#### Build phases

Travis

```yaml
before_install:
  - echo before install  
install:
  - echo install
before_script:
  - echo before script
script:
  - echo script
after_script:
  - echo after script
```

Circle

```yaml
- run:
    name: scripts
    command: |
      echo before install
      echo install
      echo before script
      echo script
      echo after script
```
