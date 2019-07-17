# circleci-conversion-chart

### TravisCI to CircleCI

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
