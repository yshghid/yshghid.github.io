+++
title = "[rna-seq] UCSC browser에서 promoter, cpgi bed 파일 만들기"
menu = "main"
+++

## 들어가며

본 포스팅에서는 아래 내용을 다룬다.

> - UCSC genome browser를 사용해서 promoter BED 파일 생성
> - CpG island BED 파일 생성

## 1. promoter BED 파일 만들기

[UCSC Table Browser](https://genome.ucsc.edu/cgi-bin/hgTables?hgsid=2329174464_GuKAhNVwI1RURLV1lILuaKkS3ckl)에 접속해준다.

다음과 같이 선택해준다.

- Assembly > GRCh38/hg38 (더 최신 버전이 있더라도 hg38 추천)
- Group > Genes and Gene prediction
- Track > Refseq Genes (또는 NCBI RefSeq)
- output format > BED- browser extensible data
-> Get output

다음과 같이 선택해준다.

- Create one BED record per > Upstream by > 2000 bases 입력
-> get BED

cf) get BED를 클릭했을때 자동으로 저장되지 않는 경우: 마우스 오른쪽 버튼을 클릭 -> "다른 이름으로 저장" 선택 -> 파일 이름 뒤에 .txt 입력 후 저장

## 2. CpG island BED 파일 만들기

[UCSC Table Browser](https://genome.ucsc.edu/cgi-bin/hgTables?hgsid=2329174464_GuKAhNVwI1RURLV1lILuaKkS3ckl)에 접속해준다.

다음과 같이 선택해준다.

- Assembly > GRCh38/hg38
- Group > Regulation
- Track > CpG islands
- output format > BED- browser extensible data
-> Get output

다음과 같이 선택해준다.

- Create one BED record per > Whole Gene
-> get BED

## 3. BED 파일 보는법

BED (Browser Extensible Data) 파일은 유전체 영역을 정의하는 파일로 .bed 또는 .txt 확장자를 가지며 3개 컬럼(염색체, 시작위치, 종료위치)을 기본 형식으로 갖는다.

위에서 생성된 bed 파일은 .txt 확장자를 가진 텍스트 파일일 것이고 열어보면 다음과 같이 구성되어있다.

```python
promoter = pd.read_csv("hg38.promoter.txt", sep="\t", names=['chr','start','end','feature','0','-'])
promoter
```
```
	chr	start	end	feature	0	-
0	chr1	67109072	67111072	XM_011541469.2_up_2000_chr1_67109073_r	0	-
1	chr1	67131227	67133227	XM_017001276.2_up_2000_chr1_67131228_r	0	-
2	chr1	67131227	67133227	XM_011541467.2_up_2000_chr1_67131228_r	0	-
3	chr1	67134970	67136970	NM_001276352.2_up_2000_chr1_67134971_r	0	-
4	chr1	67134970	67136970	NM_001276351.2_up_2000_chr1_67134971_r	0	-
...	...	...	...	...	...	...
196043	chr22_KI270733v1_random	123930	125930	NR_146119.1_up_2000_chr22_KI270733v1_random_12...	0	+
196044	chr22_KI270733v1_random	126876	128876	NR_146120.1_up_2000_chr22_KI270733v1_random_12...	0	+
196045	chr22_KI270733v1_random	135952	137952	XR_951367.3_up_2000_chr22_KI270733v1_random_13...	0	+
196046	chr22_KI270733v1_random	138978	140978	XR_001756152.2_up_2000_chr22_KI270733v1_random...	0	+
196047	chr22_KI270733v1_random	174046	176046	XM_047442803.1_up_2000_chr22_KI270733v1_random...	0	-
196048 rows × 6 columns
```

위에서 언급했듯 1, 2, 3번째 컬럼은 염색체, 시작위치, 종료위치를 나타낸다. 

4번째 컬럼은 "XM_011541469.2_up_2000_chr1_67109073_r"와 같은 형식인데 "XM_011541469.2"는 전사체 id이며 "up_2000_chr1_67109073_r"은 (XM_011541469.의 2000bp 상류라는) 염색체상 위치를 나타낸다.

5번째 컬럼은 해당 영역의 중요도를 나타내는 점수이며 생략될수있다. 6번째 컬럼은 strand(방향성)을 나타낸다. +는 양(+)의 방향(전방향)으로 DNA 가닥을 따라가고, -는 음(-)의 방향(역방향)으로 가닥을 따라가는 것을 의미한다. 이 정보는 해당 프로모터 또는 유전자 영역이 어느 DNA 가닥에 위치하는지를 나타내기 때문에 이는 유전자 발현 조절을 분석할때 중요한 정보가 될수있다. 

BED 파일의 기본 형식은 첫번째, 두번째, 세번째 컬럼에 각각 염색체, 시작위치, 종료위치 정보를 갖는 것이다. 이 형식만 만족해도 .bed 확장자 파일 Integrative Genomics Viewer (IGV) 상에 로드가 가능하다. 참고로 생성한 hg38.promoter.bed를 igv에 띄워보면 아래와 같이 로드된다.

![image](https://github.com/user-attachments/assets/1a2ec4e7-ab45-4f88-a7bb-243e73a89f2c)

참고링크 - https://nbis-workshop-epigenomics.readthedocs.io/en/latest/content/tutorials/methylationSeq/Seq_Tutorial.html
