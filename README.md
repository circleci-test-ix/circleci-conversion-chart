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
    key: node_modules-{{ checksum "package-lock.json" }}
    paths:
      - node_modules

- restore_cache:
    key: node_modules-{{ checksum "package-lock.json" }}
```

#### Set environment variables in bash

Travis

```yaml
export VAR=1
VAR2="abc"
export VAR3="${VAR2}def"
```

Circle

```yaml
export VAR=1
echo 'export VAR1=1' >> $BASH_ENV
VAR2="abc"
echo 'export VAR3='"${VAR2}def" >> $BASH_ENV
```

#### Use mac xcode 10

Travis

```yaml
os: osx
osx_image: xcode10
```

Circle

```yaml
macos:
  xcode: "10.0"
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

or 

```yaml
machine:
  image: ubuntu-1604:201903-01
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

#### Use specific node version

Travis

```yaml
language: node_js
node_js:
  - 11
```

Circle

```yaml
docker:
  - image: circleci/node:11
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

#### Use Postgres

Travis

```yaml
services:
  - postgresql
```

Circle

```yaml
docker:
  - image: circleci/postgres
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

#### Stages and Workflows

Travis

```yaml
jobs:
  include:
    - stage: build
      script: ./build
      
    - stage: test
      script: ./test-1
      script: ./test-2      
```

Circle

```yaml
jobs:
  build:
    steps:
      - run: ./build
  test1:
    steps:
      - run: ./test-1
  test2:
    steps:
      - run: ./test-2
        
workflows:
  version: 2
  build_and_test:
    jobs:   
      - build
      - test1:
          requires:
            - build
      - test2:
          requires:
            - build            
```
