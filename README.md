# TravisCI to CircleCI Conversion Chart

#### Git clone depth=1

Travis

```yaml
git:
  depth: 1
```

Circle

```yaml
orbs:
  shallow-checkout: datacamp/shallow-checkout@0.0.1
  
jobs:      
  build:    
    steps:
      - shallow-checkout/checkout
```

#### Multi-line script

Travis

```yaml
script:  
  - |
    echo line1
    echo line2
```

Circle

```yaml
- run: |
    echo line1
    echo line2
```

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

Circle using Docker

```yaml
docker:
  - image: circleci/python:3.6

# may need this if you're using pip
steps:
  - run: echo 'export PATH=/home/circleci/.local/bin:$PATH' >> $BASH_ENV  
```

or Circle using machine

```yaml
machine:
  image: ubuntu-1604:201903-01 # this has python 3.5 installed

# may need this if you're calling python3
steps:
  - run: pyenv global 3.5.2
```

#### Use Postgres

Travis

```yaml
# runs on localhost:5432 as user postgres
services:
  - postgresql
```

Circle

```yaml
# runs on localhost:5432 as user postgres
docker:
  - image: circleci/postgres
```

#### Use MySQL

Travis

```yaml
# runs on localhost:3306
services:
  - mysql
```

Circle

```yaml
# runs on localhost:3306
docker:
  - image: circleci/mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: rootpw
      MYSQL_DATABASE: db
      MYSQL_USER: user
      MYSQL_PASSWORD: password
```

#### Use MongoDB

Travis

```yaml
# runs on localhost:27017
services:
  - mongodb
```

Circle

```yaml
# runs on localhost:27017
docker:
  - image: circleci/mongo:4
```

#### Install APT Packages

Travis

```yaml
addons:
  apt:
    packages:
    - 7zip
    - unrar
```

Circle

```yaml
sudo apt-get install 7zip unrar
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

#### Matrix Builds

Travis

```yaml
language: python
matrix:
  include:
  - python: "2.7"
    env: TEST_SUITE=suite_2_7
  - python: "3.3"
    env: TEST_SUITE=suite_3_3
  - python: "pypy"
    env: TEST_SUITE=suite_pypy
script: ./test.py $TEST_SUITE    
```

Circle

```yaml
# Use YAML aliases
run_script: &run_script
  - run: ./test.py $TEST_SUITE

jobs:
  python_2_7:
    environment:
      TEST_SUITE: "2.7"
    steps:
      - <<: *run_script
  python_3_3:
    environment:
      TEST_SUITE: "3.3"
    steps:
      - <<: *run_script
  python_py_py:
    environment:
      TEST_SUITE: "pypy"
    steps:
      - <<: *run_script        
```
