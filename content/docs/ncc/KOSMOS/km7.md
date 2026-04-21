---
date : 2026-04-13
tags: ['2026-04']
categories: ['kosmos']
bookHidden: true
title: "폐쇄망 Windows 환경세팅 #1 패키지 오프라인 설치하기"
---

# 폐쇄망 Windows 환경세팅 #1 패키지 오프라인 설치하기

#2026-04-13

---


#1 환경 확인

```bash
#1 파이썬 버전 확인
python --version  # Python 3.10.x이어야함.

#2 pip 버전 확인
python -m pip --version
```

###

#2 패키지 오프라인 설치


pip install pandas를 입력하면 pip는 인터넷을 통해 PyPI라는 공식 파이썬 패키지 저장소 서버에 접속한다. 그 서버에서 pandas 설치 파일을 찾아서 내 PC로 다운로드하고 압축을 풀어서 Python 폴더 안에 집어넣는다.

폐쇄망은 인터넷 연결 자체가 없어서 pip가 PyPI 서버에 접속하려 해도 그냥 실패한다. 그래서 PyPI 서버 대신 내 PC 안의 특정 폴더를 저장소로 쓴다 즉 그 폴더 안에 설치 파일들을 미리 넣어둔다.


```bash
pip install pandas                              ← 인터넷에서 받아와서 설치
pip install --no-index --find-links=C:\폴더\ pandas  ← 이 폴더에서 찾아서 설치
```

###

```plain text
C:\설치파일\hana_ml\
    ├── hana_ml-2.17.23062800-py3-none-any.whl
    ├── hdbcli-2.17.14-cp36-abi3-win_amd64.whl
    ├── pandas-2.0.2-cp310-cp310-win_amd64.whl
    ├── numpy-1.25.0-cp310-cp310-win_amd64.whl
    └── ... (나머지 .whl 파일들)
```

hana_ml 폴더 안에 whl 설치 파일들이 있는데 패키지마다 의존성이 다르다. 가장 아무것도 필요로 하지 않는 패키지부터 설치하고, 점점 위로 올라가야 한다. six, packaging, pyparsing 같이 혼자 독립적으로 동작하는 것들을 먼저 깔고 그 다음에 numpy를 깔고, 그 다음에 pandas와 matplotlib을 깔고 마지막으로 hana_ml을 설치한다.


###

#cf

hana_ml/ 폴더는 원래 hana_ml을 설치하기 위해 준비된 것이다. NGS 노트북들은 추가로 PyPDF2, openpyxl, Jupyter Notebook도 필요한데 이 파일들은 폴더 안에 없다.

설치하려면 인터넷이 되는 PC에서 pip download 명령어를 쓰면 설치는 하지 않고 .whl 파일만 특정 폴더에 받아둘 수 있다. 

```bash
pip download PyPDF2==3.0.1 -d C:\다운로드폴더\
```

이렇게 받은 파일을 USB에 담아서 폐쇄망 PC로 옮기고, 동일한 방식으로 오프라인 설치하면 된다.

###

```bash
python -c "import hana_ml; import pandas; import numpy; import PyPDF2; import openpyxl; print('모든 패키지 설치 완료')"
```

설치를 마친 뒤에는 실제로 import가 되는지 확인한다. ModuleNotFoundError가 안뜨면 패키지 설치가 완료된것이다.

###

#3 실제 설치하기

```plain text
hana_ml
├── pandas          ← numpy에 의존
│   ├── numpy
│   ├── python-dateutil  ← six에 의존
│   │   └── six
│   ├── pytz
│   └── tzdata
├── matplotlib      ← numpy, Pillow 등에 의존
│   ├── numpy
│   ├── contourpy   ← numpy에 의존
│   ├── cycler
│   ├── fonttools
│   ├── kiwisolver
│   ├── packaging
│   └── pyparsing
├── tqdm
├── colorama
├── wrapt
├── Deprecated      ← wrapt에 의존
├── pydotplus       ← pyparsing에 의존
└── htmlmin
```

위 의존성을 고려해서 설치한다.

```bash
#1 cmd를 관리자 권한으로 실행
cd C:\설치파일\hana_ml

#2 가장 기반이 되는 패키지부터 설치
pip install --no-index --find-links=. six-1.16.0-py2.py3-none-any.whl
pip install --no-index --find-links=. packaging-23.1-py3-none-any.whl
pip install --no-index --find-links=. pyparsing-3.1.0-py3-none-any.whl
pip install --no-index --find-links=. pytz-2023.3-py2.py3-none-any.whl
pip install --no-index --find-links=. tzdata-2023.3-py2.py3-none-any.whl
pip install --no-index --find-links=. colorama-0.4.6-py2.py3-none-any.whl
pip install --no-index --find-links=. cycler-0.11.0-py3-none-any.whl

#3 numpy (많은 패키지가 의존)
pip install --no-index --find-links=. numpy-1.25.0-cp310-cp310-win_amd64.whl

#4 numpy에 의존하는 패키지들
pip install --no-index --find-links=. contourpy-1.1.0-cp310-cp310-win_amd64.whl
pip install --no-index --find-links=. kiwisolver-1.4.4-cp310-cp310-win_amd64.whl
pip install --no-index --find-links=. fonttools-4.40.0-cp310-cp310-win_amd64.whl
pip install --no-index --find-links=. Pillow-9.5.0-cp310-cp310-win_amd64.whl

#5 $pandas
pip install --no-index --find-links=. python_dateutil-2.8.2-py2.py3-none-any.whl
pip install --no-index --find-links=. pandas-2.0.2-cp310-cp310-win_amd64.whl

#6 matplotlib
pip install --no-index --find-links=. matplotlib-3.7.1-cp310-cp310-win_amd64.whl

#7 나머지 유틸 패키지
pip install --no-index --find-links=. tqdm-4.65.0-py3-none-any.whl
pip install --no-index --find-links=. schedule-1.2.0-py2.py3-none-any.whl
pip install --no-index --find-links=. wrapt-1.15.0-cp310-cp310-win_amd64.whl
pip install --no-index --find-links=. Deprecated-1.2.14-py2.py3-none-any.whl

#8 .tar.gz 파일 (소스 설치): htmlmin, pydotplus는 .tar.gz 형식입니다. 설치 방법은 동일합니다.
pip install --no-index --find-links=. htmlmin-0.1.12.tar.gz
pip install --no-index --find-links=. pydotplus-2.0.2.tar.gz
# .tar.gz 설치 시 Visual C++ Build Tools가 없으면 오류가 날 수 있습니다. 오류 발생 시 아래 참고.

#9 hdbcli (HANA DB 드라이버)
pip install --no-index --find-links=. hdbcli-2.17.14-cp36-abi3-win_amd64.whl

#10 hana_ml 본체 (마지막에 설치)
pip install --no-index --find-links=. hana_ml-2.17.23062800-py3-none-any.whl

#cf --find-links 한 줄 일괄 설치
pip install --no-index --find-links=C:\설치파일\hana_ml hana_ml==2.17.23062800
# pip가 의존성을 파악해서 같은 폴더 내에서 자동으로 찾아 설치.
```

추가로 필요한 패키지를 받으려면?

```bash
#1 인터넷 되는 PC에서 다운로드
pip download PyPDF2==3.0.1 -d C:\다운로드\
pip download openpyxl==3.1.2 -d C:\다운로드\
pip download pdfminer.six==20221105 -d C:\다운로드\
pip download notebook -d C:\다운로드\

#2 다운로드된 .whl 파일을 폐쇄망 PC로 복사 후
pip install --no-index --find-links=C:\다운로드\ PyPDF2==3.0.1
pip install --no-index --find-links=C:\다운로드\ openpyxl==3.1.2
pip install --no-index --find-links=C:\다운로드\ pdfminer.six==20221105
pip install --no-index --find-links=C:\다운로드\ notebook
```

###

설치 완료 확인하려면?

```bash
python -m pip list

# 아래가 뜨는지 확인하기.
Package        Version
-------------- ----------------
hana-ml        2.17.23062800
hdbcli         2.17.14
pandas         2.0.2
numpy          1.25.0
PyPDF2         3.0.1
openpyxl       3.1.2
matplotlib     3.7.1

# 한번에 import 해보기.
python -c "import hana_ml; import pandas; import numpy; import PyPDF2; import openpyxl; print('모든 패키지 설치 완료')"
```