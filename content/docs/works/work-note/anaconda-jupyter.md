+++
title = "jupyter notebook 설치 후 python, R 가상환경 연결하기"
menu = "main"
categories = ["etc"]
+++

# jupyter notebook 설치 후 python, R 가상환경 연결하기

## 들어가며

본 포스팅에서는 아래 내용을 다룬다.
> - mac os에서 anaconda로 jupyter notebook 설치 후 웹으로 실행하기
> - python 가상환경을 생성하고 ipykernel로 jupyter notebook과 연결
> - r 가상환경을 생성하고 irkernel로 jupyter notebook과 연결

## 1. Jupyter notebook 설치

### 1.1 anaconda 다운

Anaconda의 공식 웹사이트(https://www.anaconda.com/)에서 최신 버전의 Anaconda를 다운로드한다. Download for Mac 에서 Download for Apple Silicon 을 선택해준다. [이 링크](https://support.apple.com/ko-kr/116943)를 확인해보고 자신의 맥북 기종에 맞게 선택해주면 된다.

Mac OS의 경우 다운로드된 파일을 실행하고, 디폴트 설정을 유지하면서 `계속`을 해주면 설치가 완료된다.

### 1.2 jupyter notebook 다운, 실행

터미널을 열어서 아래 명령어를 입력한다.

```
$ pip install jupyter
```

python을 실행하려는 작업 디렉토리로 이동하고 jupyter notebook을 입력한다.

```
$ pwd
/Users/yshmbid/Documents/home/bin

$ jupyter notebook

[I 2024-08-06 10:52:17.372 LabApp] JupyterLab extension loaded from /opt/anaconda3/lib/python3.9/site-packages/jupyterlab
[I 2024-08-06 10:52:17.372 LabApp] JupyterLab application directory is /opt/anaconda3/share/jupyter/lab
[I 10:52:17.376 NotebookApp] Serving notebooks from local directory: /Users/yshmbid/Documents/home
[I 10:52:17.376 NotebookApp] Jupyter Notebook 6.4.12 is running at:
[I 10:52:17.376 NotebookApp] http://localhost:8888/?token=933c362bff3bdd602aafc951e4eca6ffbe78b05aa7fb7bab
[I 10:52:17.376 NotebookApp]  or http://127.0.0.1:8888/?token=933c362bff3bdd602aafc951e4eca6ffbe78b05aa7fb7bab
[I 10:52:17.376 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[C 10:52:17.380 NotebookApp]
```

이 명령어를 입력하면 웹 브라우저가 자동으로 열리며, Jupyter Notebook 인터페이스가 표시된다. 만약 브라우저가 자동으로 열리지 않는다면, 터미널에 표시된 URL(보통 http://localhost:8888/)을 복사하여 브라우저에 직접 입력해도 된다. 
새 노트북을 생성하려면 New > Python 3 을 클릭한다.

## 2. python 환경 세팅

### 2.1 python 가상환경 만들기

이번에는 jupyter notebook 커널과 가상환경을 연결해서 jupyter notebook에서 가상 환경을 사용할 수 있도록 하는 작업을 할것이다. 가상 환경(Virtual Environment)은 특정 프로젝트에서 사용되는 패키지와 라이브러리의 버전을 관리할 수 있도록 돕는 기능이다. 이를 통해 서로 다른 프로젝트 간의 패키지 충돌을 방지하고 각 프로젝트가 독립적인 환경에서 동작할 수 있도록 할수있다.

conda 명령어를 이용해 workspace라는 가상 환경을 만들고, python=3.8을 설치하는 명령어는 다음과 같다.

```
$ conda create --name workspace python=3.8
```

생성된 가상 환경을 사용하기 위해 활성화한다.

```
(base) $ conda activate workspace
(workspace) $
```

$ 앞이 base 에서 workspace로 바뀐 것을 확인할 수 있다. 파이썬 버전을 확인해본다.

```
(workspace) $ python --version
Python 3.8.19
```

생성된 가상 환경 리스트를 확인해본다.

```
(workspace) $ conda env list
# conda environments:
#
base                     /opt/anaconda3
workspace             *  /opt/anaconda3/envs/workspace
```

아래 코드로 필수 라이브러리를 설치한다.

```
(workspace) $ conda install scipy numpy matplotlib jupyter-notebook pip pandas scikit-learn seaborn
```

문제없이 설치되었다면 다음으로 넘어간다. 나의 경우 jupyter notebook 설치에서 아래 오류가 발생하였고

```
PackagesNotFoundError: The following packages are not available from current channels:

  - jupyter-notebook

Current channels:

  - https://conda.anaconda.org/conda-forge/osx-64
  - https://conda.anaconda.org/conda-forge/noarch
  - https://conda.anaconda.org/bioconda/osx-64
  - https://conda.anaconda.org/bioconda/noarch
  - https://repo.anaconda.com/pkgs/main/osx-64
  - https://repo.anaconda.com/pkgs/main/noarch
  - https://repo.anaconda.com/pkgs/r/osx-64
  - https://repo.anaconda.com/pkgs/r/noarch

To search for alternate channels that may provide the conda package you're
looking for, navigate to

    https://anaconda.org

and use the search bar at the top of the page.
```

따라서 pip install jupyter 명령어로 따로 설치해주었다.

```
(workspace) $ pip install jupyter
```

### 2.2 python 커널 등록

생성한 workspace 가상환경을 jupyter notebook 커널로 설정할 수 있게 연결해줘야 한다. 먼저 가상 환경에 ipykernel 설치하기 위해 workspace가 활성화된 상태로 다음 명령어를 실행한다.

```
(workspace) $ pip install ipykernel
```

다음으로 가상환경 workspace를 jupyter notebook의 커널로 추가한다.

```
(workspace) $ python -m ipykernel install --user --name workspace --display-name workspace
```

여기서 --name은 커널의 내부 이름을 지정하고, --display-name은 jupyter notebook에서 표시될 이름을 지정한다.
jupyter kernelspec list를 사용하면 설치된 커널의 이름들을 볼 수 있는데 여기서 나열되는 이름이 바로 --name으로 지정한 이름이다. 
그리고 jupyter Notebook을 실행했을 때, 노트북을 새로 만들거나 커널을 변경할 때 표시되는 이름이 --display-name로 지정한 이름이다. 

위 코드는 둘을 통일했는데 아래와 같이 주피터 노트북상의 커널 이름은 직관적으로 파이썬 버전 이름으로 작성해줘도 된다.

```
(workspace) $ python -m ipykernel install --user --name workspace --display-name python3.8
```

python을 실행하려는 작업 디렉토리로 이동하고 jupyter notebook을 입력한다.

```
(workspace) $ pwd
/Users/yshmbid/Documents/home/bin

(workspace) $ jupyter notebook
```

터미널창과 함께 웹페이지가 호출되면 New 를 클릭해보자. 생성한 가상환경 workspace가 뜨는 것을 확인할 수 있다. 
workspace를 클릭해주면 앞서 세팅한 workspace의 파이썬 버전과 설치한 필수 라이브러리들이 적용된 파이썬 환경에서 작업할 수 있게 된다. 

pandas와 numpy 라이브러리를 테스트 삼아 import 해보면 잘 import 되는것을 확인할 수 있다. 참고로 종료를 원한다면 터미널에서 ctrl+c 를 두번 눌러주면 된다. 

## 3. R 환경 세팅

### 3.1 R 가상환경 만들기

conda 명령어를 이용해 rspace라는 가상 환경을 만들고, r-version 4.0.5를 설치하는 명령어는 다음과 같다. 버전 4.0.5를 설정한 이유는 더 높은 버전을 설정해도 되긴 하지만 여러 패키지를 설치해본 결과 4.0.5 버전이 가장 패키지 충돌이 적어서 추천한다.
```
$ conda create --name rspace r-base=4.0.5
```

생성된 가상 환경을 사용하기 위해 활성화한다.

```
$ conda activate rspace
```

생성된 가상 환경 리스트를 확인해본다.

```
(rspace) $ conda env list
# conda environments:
#
base                     /opt/anaconda3
rspace                *  /opt/anaconda3/envs/rspace
workspace                /opt/anaconda3/envs/workspace
```

아래 코드로 필수 라이브러리를 설치하고, R 및 주피터 관련 패키지들도 설치해준다.

```
(rspace) $ conda install -c r r-essentials
(rspace) $ conda install -c conda-forge r-irkernel r-devtools
```

문제없이 설치되었다면 다음으로 넘어간다. 나의 경우 아래 오류가 발생하였고

```
(rspace) $ conda install -c r r-essentials
Collecting package metadata (current_repodata.json): done
Solving environment: failed with initial frozen solve. Retrying with flexible solve.
Solving environment: failed with repodata from current_repodata.json, will retry with next repodata source.
Collecting package metadata (repodata.json): done
Solving environment: / Killed: 9
```

따라서 아래 코드로 재설치해주었는데

```
(rspace) $ conda install -c conda-forge r-essentials
Collecting package metadata (current_repodata.json): done
Solving environment: failed with initial frozen solve. Retrying with flexible solve.
Solving environment: failed with repodata from current_repodata.json, will retry with next repodata source.
Collecting package metadata (repodata.json): done
Solving environment: / Killed: 9
```

또다시 오류가 발생해서.. 그냥 jupyter만 설치하고 나머지는 필요한 패키지만 R 인터프리터 상에서 설치해주기로 했다. (메모리 부족 문제일 수 있다고 함.)

```
(rspace) $ brew install jupyterlab
```

### 3.2 R 커널 등록

rspace가 활성화된 상태로 터미널에서 R 인터프리터를 실행하고, IRkernel을 설치 후 Jupyter에 커널을 등록한다.

```
(rspace) $ R
```
```
> install.packages('IRkernel')
> IRkernel::installspec(name = 'rspace', displayname = 'rspace')
```

매우 오래걸렸지만 문제없이 설치되었다.

터미널에서 R을 실행하려는 작업 디렉토리로 이동하고 jupyter notebook을 입력한다.

```
(rspace) $ pwd
/Users/yshmbid/Documents/home/bin

(rspace) $ jupyter notebook
```

터미널창과 함께 웹페이지가 호출되면 New 를 클릭한다. 생성한 가상환경 rspace가 뜨는 것을 확인할 수 있다.
rspace를 클릭해주면 앞서 세팅한 rspace의 R 버전과 설치한 필수 라이브러리들(나는 못했지만)이 적용된 R 환경에서 작업할 수 있게 된다!

## 마무리

미처 설치하지 못한 필수 라이브러리들을 터미널의 R 인터프리터 상에서 설치해주자. 주피터 노트북 상에서 설치해줄수도 있긴 한데, 왜인지 모르겠지만 R 패키지 설치는 터미널로 R 열어서 설치하는게 오류도 잘 안나고 빠르다.

```
(rspace) $ R
```
```
> install.packages("tidyverse")
> install.packages("devtools")
> install.packages("ggplot2")
> install.packages("dplyr")
```
