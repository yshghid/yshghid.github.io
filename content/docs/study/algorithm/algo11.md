---
date : 2025-08-05
tags: ['2025-08']
categories: ['python']
bookHidden: true
title: "Python #1 가상환경 구성 및 패키지 관리"
---

# Python #1 가상환경 구성 및 패키지 관리

#2025-08-05

---

### 1. 개념

#1 가상환경의 필요성?

우리가 파이썬을 사용할 때, 가장 먼저 겪게 되는 문제 중 하나는 바로 패키지 버전 충돌이다. 예를 들어 어떤 프로젝트에서는 numpy==1.18.5 버전을 사용하고 있고, 또 다른 프로젝트에서는 numpy==1.24.0 버전을 사용하고 있다고 하면 이 둘을 동시에 하나의 환경에 설치하게 되면 충돌이 일어나거나 예상치 못한 에러가 발생할 가능성이 커진다. 특히 머신러닝, 데이터분석, 웹개발 프로젝트를 하다 보면 프로젝트마다 사용하는 패키지와 버전이 다르기 때문에 이러한 문제는 일상적으로 발생하며 따라서 각 프로젝트가 독립적으로 실행될 수 있는 ‘가상환경(Virtual Environment)’을 만들어서 관리해야 한다.

#2 가상환경이란?

가상환경은 시스템의 전역 파이썬 환경과 격리된 공간으로, 이 환경 안에서는 독립된 파이썬 인터프리터와 독립된 패키지 디렉토리를 갖게 되기 때문에 다른 프로젝트의 영향을 받지 않는다. 이를 가능하게 해주는 대표적인 도구가 venv 라는 표준 파이썬 모듈이다.

#3 가상환경의 생성방법?

터미널에서 python -m venv myenv 라는 명령어를 입력하면, myenv라는 이름의 폴더가 생성되고 폴더 안에는 bin, lib, include, 그리고 pyvenv.cfg 파일이 들어 있다. bin 안에는 실행 파일들이 있고, lib 안에는 이 가상환경에 설치된 패키지들이 들어간다. 만든 가상환경을 활성화하려면 macOS나 Linux에서는 source myenv/bin/activate, Windows에서는 myenv\Scripts\activate 를 입력하면 터미널 프롬프트가 (myenv)처럼 바뀌어서 이 가상환경 안에서만 모든 패키지가 설치되고 실행되게된다.

#4 가상환경의 활용방법?

가상환경 안에서는 이제 마음대로 패키지를 설치할 수 있는데 예를 들어 pip install numpy pandas를 입력하면 이 환경 안에만 numpy와 pandas가 설치되고 시스템 전역에는 전혀 영향을 주지 않는다. 특히 협업할 때 유용한데 나 혼자 코드를 실행하는 게 아니라 팀원들과 코드를 공유해야 하는 상황이라면 모든 팀원이 동일한 버전의 패키지를 설치해야 하는데 이럴 때 requirements.txt 파일이 유용하다.

requirements.txt는 현재 가상환경에 설치된 모든 패키지와 그 버전 정보를 담고 있다. pip freeze > requirements.txt 하면 numpy==1.24.0 이런 형식으로 패키지 목록이 쭉 저장된다. 이 파일을 GitHub에 같이 올려두고 다른 팀원이 그 코드를 받아서 실행할 때는 pip install -r requirements.txt 명령어 하나만 입력하면 동일한 환경이 복제된다. 

pip은 파이썬 패키지를 설치하는 도구인데 파이썬 생태계에서 pip install이 apt install, brew install처럼 시스템에서 소프트웨어를 설치하는 명령어이다. pip install numpy를 입력하면 파이썬 공식 패키지 저장소인 PyPI(Python Package Index)에서 numpy를 다운로드하고 설치한다. 특정 버전을 설치하고 싶으면 pip install numpy==1.18.5처럼 버전 지정도 가능하고, 최신 버전으로 업그레이드하고 싶으면 pip install --upgrade numpy처럼 사용할 수 있다.

가상환경을 생성하면 이 환경 안에서만 설치한 패키지들이 인식되는데 만약 이를 Jupyter Notebook이나 VS Code와 같은 툴에서 사용하고 싶다면 이 가상환경을 해당 툴에 연결해줘야 한다. 예를 들어 Jupyter에서는 ipykernel 패키지를 설치한 후 python -m ipykernel install --user --name=myenv 명령어를 통해 Jupyter에서 이 가상환경을 커널로 선택할 수 있게 해줘야 하고 VS Code에서도 .venv 폴더를 자동으로 인식하도록 settings.json 파일에서 python.pythonPath를 지정해주거나, Python 확장을 통해 커맨드 팔레트에서 가상환경을 선택해줘야한다.

#5 가상환경 사용시 유의점?

첫째, 프로젝트 루트 폴더에 .venv, env, 또는 venv와 같은 이름으로 가상환경을 생성하는게 좋다. 이렇게 하면 프로젝트와 가상환경이 명확하게 구분되고 자동화 도구들이 이 디렉터리를 인식하기 쉽다. 둘째, .gitignore 파일에 가상환경 폴더를 반드시 추가해야 하는데 왜냐하면 가상환경 폴더는 수백 MB 이상으로 무겁고 플랫폼마다 실행 파일 구조가 달라서 Git으로 버전 관리하면 오히려 오류의 원인이 된다.

#6 (내가조아하는) conda??

conda는 Anaconda나 Miniconda라는 배포판과 함께 제공되는 툴인데 pip보다는 무겁지만 데이터 분석 라이브러리 설치가 쉽고 venv보다 더 다양한 언어와 라이브러리를 통합해서 관리할 수 있다. 

### 2. 실습

#1 가상환경 구성 및 패키지 관리

가상환경은 파이썬 표준 모듈인 venv를 이용해서 쉽게 만들 수 있다.


```shell
# 가상환경 생성
(base) $ python -m venv myenv
```

myenv라는 디렉터리가 생성된다. 구조는 다음과 같다:

```plain text
myenv/
├── bin/            # 실행파일
├── lib/            # 설치된 패키지
└── pyvenv.cfg      # 환경 정보
```

```python
# 가상환경 활성화 - macOS/Linux
(base) $ source myenv/bin/activate
(myenv) $ # 프롬프트 앞에 (myenv)가 붙으면 성공

# 가상환경 비활성화
(myenv) $ deactivate
```

```shell
# 가상환경에서 패키지 설치
$ pip install numpy pandas
$ pip list
```
```plain text
>>
Package    Version
---------- -------
numpy      1.24.0
pandas     2.0.3
```
```shell
# requirements.txt로 환경 공유하기

# 현재 설치된 패키지 목록 저장
$ pip freeze > requirements.txt
```
```plain text
# requirements.txt

numpy==1.24.0
pandas==2.0.3
```
```bash
# 다른 환경에서 복제하기

$ python -m venv newenv
$ source newenv/bin/activate
$ pip install -r requirements.txt
```

가상환경을 새로 만들고, 이 파일을 기반으로 설치할 수 있어서 이걸 통해 모든 팀원이 동일한 환경에서 개발을 진행할 수 있다.

```shell
# .gitignore에 가상환경 제외하기
myenv/
```

가상환경 디렉터리는 Git으로 공유하면 안되므로 .gitignore에 myenv/를 추가하고 requirements.txt 파일만 공유한다.

>  VS Code에서 가상환경 연동하기

1. VS Code 좌측 하단에서 Python 버전 클릭
2. 가상환경 위치 선택 (myenv/bin/python 또는 myenv\Scripts\python.exe)
3. .vscode/settings.json에 자동 설정됨

> 가상환경 + Jupyter 노트북 사용하기

```shell
# Jupyter에서 가상환경 커널 등록
$ pip install ipykernel
$ python -m ipykernel install --user --name=myenv
```
하면 JupyterLab이나 Jupyter Notebook에서 myenv라는 커널을 선택할 수 있다. 

> 실제 프로젝트 예제 – 데이터분석 환경 구성

* 프로젝트 디렉토리 구조는 아래와 같음.
* 환경 설정 및 필수 패키지 설치하기.

```plain text
project-folder/
└── (empty)
```

#수행

```shell
$ python -m venv venv
$ source venv/bin/activate
$ pip install numpy pandas matplotlib seaborn
$ pip freeze > requirements.txt
```
#결과

```plain text
project-folder/
├── venv/           
│   ├── bin/          
│   ├── lib/           
│   └── ... (기타 구성 파일)
└── requirements.txt 
```