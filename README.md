# setting_elixir_env_rocky8
Rocky Linux 8에서 elixir 개발 환경을 준비 기록

## 사전 준비
### 패키지 설치 (dnf)
#### Erlang dependency
- make
- gcc or clang
- ncurses-devel
- sed
- openssl (>=0.9.8)
- java jdk (>=1.6.0): jinterface를 위해 필요.
- erlang-jinterface: jinterface를 위해 필요
- libiodbc-devel: jinterface를 위해 필요
- erlang-wx: wx가 내장 라이브러리 지원을 위해 필요
- wxBase3-devel: wx가 내장 라이브러리 지원을 위해 필요
- wxGTK3*: wx가 내장 라이브러리 지원을 위해 필요

#### Elixir Dependency


### 빌드
- [Erlang OTP](https://www.erlang.org/downloads): 원하는 버전으로 빌드
  - 공식문서: https://www.erlang.org/doc/installation_guide/install
- [Elixir](https://github.com/elixir-lang/elixir/releases): 빌드한 Erlang OTP와 호환되는 것으로 설치해야함.
  - 공식문서: https://elixir-lang.org/install.html#compiling-from-source

#### Erlang
```bash
# 1. 압축해제
tar fxz otp_src_"$version".tar.gz

# 2. 소스 root로 이동
cd otp_src_"$version"


# 3. ERL_TOP 환경 변수 설정
export ERL_TOP=/path/to/top_src_"$version"  # 소스 루트

# 4. 테스트 dir 생성
make release_tests

# 5. 테스트 수행
cd release/tests/test_server
$ERL_TOP/bin/erl -s ts install -s ts smoke_test batch -s init stop

# 6. 빌드
cd $ERL_TOP
make install


# 7. 환경 변수 설정
echo 'export PATH=$ERL_TOP/lib/erlang/bin:$PATH' >> ~/.bashrc 
echo 'export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/$ERL_TOP/lib/erlang/lib' >> ~/.bashrc
source ~/.bashrc  # 현재 shell의 환경 변수 갱신

# 8. Erlang 버전 확인
erl -eval 'erlang:display(erlang:system_info(otp_release)), halt().'  -noshell
# 참고: https://stackoverflow.com/questions/9560815/how-to-get-erlangs-release-version-number-from-a-shell

```

- `$ERL_TOP/bin`은 `$ERL_TOP/lib/erlang/bin` 안에 있는 binary를 symbolic link로 가지고 있다.

## Elixir 빌드
```bash
# 1. 설치 원하는 지정
mkdir ~/elixir
cd ~/elixir

# 2. source code 다운로드, 링크는 미래에 업데이트 되면 알아서 수정반영할 것.
wget https://github.com/elixir-lang/elixir/archive/refs/tags/v1.16.2.tar.gz

# 3. 압축 해제 & 이름 변경
tar fxz v1.16.2.tar.gz
mv elixir-1.16.2 1.16.2  # erlang과 비슷하게 버전 이름만 남김, 이미 상위 dir로 elixir임을 알 수 있기 때문
cd 1.16.2

# 4. Makefile 수정
sed -i '1s|PREFIX ?= /usr/local|PREFIX ?= .|' Makefile   # 현재 사용자만 사용하도록 기본 prefix 수정

# 5. 환경 변수 설정
echo "export PATH=$(pwd)/bin:"'$PATH' >> ~/.bashrc
echo 'export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:'"$(pwd)/lib" >> ~/.bashrc
source ~/.bashrc

# 6. 버전 확인
elixir --vesion

```
- elixir는 erlang 준비 후 사용할 수 있다.
- 컴파일하려면 erlang 바이너리 경로를 알 고 있어야 한다.