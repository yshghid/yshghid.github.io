+++
title = "[Tool] edgeR"
menu = "main"
+++

# edgeR pipeline

## 1. 사전 설치

아래 코드로 edgeR을 설치해준다. baySeq을 데이터셋으로 사용해줄 것이므로 baySeq도 같이 설치해준다.

```r
if (!require("BiocManager", quietly = TRUE))
    install.packages("BiocManager")
BiocManager::install("edgeR")
BiocManager::install("baySeq") 
```

설치가 완료되었다면 R 환경과 edgeR, baySeq 패키지의 버전을 확인해보자. 

```r
R.version
packageVersion("edgeR")
packageVersion("baySeq")
```
```
               _
platform       x86_64-conda-linux-gnu
arch           x86_64
os             linux-gnu
system         x86_64, linux-gnu
status
major          4
minor          0.5
year           2021
month          03
day            31
svn rev        80133
language       R
version.string R version 4.0.5 (2021-03-31)
nickname       Shake and Throw

[1] '3.32.1'

[1] '2.24.0'
```

나의 경우 R(4.0.5), edgeR(3.32.1), baySeq(2.24.0)을 사용해주었다. 참고로 edgeR은 R(>= 3.6.0), baySeq은 R(>= 2.3.0) 버전을 필요로 한다.

## 2. DE 분석

### 2.1 데이터셋 가져오기

사용할 데이터셋을 로드해준다. 앞서 말했듯 baySeq의 `mobData`를 사용할 것이다.

```R
library("baySeq")
library("edgeR")

data(mobData)

dim(mobData)
head(mobData)
```
```
3000*6
A matrix: 6 x 6 of type dbl
SL236	SL260	SL237	SL238	SL239	SL240
0	0	0	0	0	0
18	21	1	5	1	1
1	2	2	3	0	0
68	87	270	184	396	368
68	87	270	183	396	368
1	0	6	10	6	12
```

`dim(mobData)`를 확인해보면 `mobData`는 6개의 샘플에 대한 3,000개 유전자의 발현량 데이터임을 알 수 있다.

샘플이름만 봐서는 해당 샘플에 대한 정보를 알기 어렵다. `help(mobData)`를 찍어보면 해당 데이터셋의 정보를 확인할 수 있다.

```r
help(mobData)
```
![image](https://github.com/user-attachments/assets/fc99aab6-b502-4e1f-a6fc-df4d130d5c6c)


확인 결과 6개의 샘플은 각각 3개의 그룹으로 나뉜다! `mobDataGroups`로 그룹을 지정해주고, 발현랑 데이터와 그룹 정보를 `d`로 넣어준다. 유전자 이름은 간단히 g1-g3000으로 넣어주었다.

```r
mobDataGroups <- c("MM", "MM", "WM", "WM", "WW", "WW")
d <- DGEList(counts=mobData, group=factor(mobDataGroups), genes=paste0("g", 1:3000))
```

`d`는 다음과 같이 `counts`, `samples`, `genes` 정보가 담긴 `DGEList` 객체다. 

```r
head(d)
```
```
$counts
A matrix: 6 x 6 of type dbl
SL236	SL260	SL237	SL238	SL239	SL240
1	0	0	0	0	0	0
2	18	21	1	5	1	1
3	1	2	2	3	0	0
4	68	87	270	184	396	368
5	68	87	270	183	396	368
6	1	0	6	10	6	12
$samples
A data.frame: 6 x 3
group	lib.size	norm.factors
<fct>	<dbl>	<dbl>
SL236	MM	107684	1
SL260	MM	217060	1
SL237	WM	159645	1
SL238	WM	142980	1
SL239	WW	193570	1
SL240	WW	205867	1
$genes
A data.frame: 6 x 1
genes
<chr>
1	g1
2	g2
3	g3
4	g4
5	g5
6	g6
```

### 2.2 저발현 유전자 제거하기

모든 샘플에서 저발현되는 유전자들은 분석에서 제외하는 것이 좋다. 예를 들어, 모든 샘플에서 발현량이 0인 경우 해당 유전자를 제거하는 코드는 아래와 같다.

```r
non_zero_counts <- rowSums(d$counts) != 0
d <- d[non_zero_counts, ]
dim(d)
```
```
2838 6
```

필터링 결과 162개의 유전자가 제거되어 2,838개 유전자가 이후 분석에 사용되었다.

### 2.3 정규화

6개의 샘플은 라이브러리 크기가 모두 다르다. 라이브러리 크기는 다음 코드로 확인해볼수 있다.

```r
privious_lib_size <- d$samples$lib.size
privious_lib_size
```
```
107684 217060 159645 142980 193570 205867
```

이러한 불균형 때문에 RNA-Seq 데이터의 정규화는 샘플 간의 차이를 비교할 수 있도록 하기 위해 매우 중요하다. 각 샘플의 라이브러리 크기를 고려해야 올바른 분석을 수행할 수 있다. 
edgeR에서는 이 차이를 정규화하기 위해 `calcNormFactors` 함수를 사용하여 라이브러리 크기에 대한 스케일링 팩터(비례 계수)를 계산하는데, 계산된 스케일링 팩터는 다음과 같다.

```r
d <- calcNormFactors(d)
d$samples$norm.factors
```
```
0.9676656 0.9544311 0.8880339 0.8671801 1.2238652 1.1488350
```

이후 분석에서는 계산한 스케일링 펙터를 원래 라이브러리 크기에 곱한 값 `effective_lib_size`이 적용된다.

```r
effective_lib_size <- d$samples$lib.size * d$samples$norm.factors
effective_lib_size
```
```
104202.1 207168.8 141770.2 123989.4 236903.6 236507.2
```

앞서 `d$samples$norm.factors`가 모두 1이었던 것을 다시 확인해보자! `calcNormFactors`를 수행하지 않으면 `d$samples$norm.factors`는 모두 1이다.

### 2.4 데이터 시각화

다차원 척도법(Multidimensional Scaling, MDS)을 사용하여 샘플 간 관계를 시각화할수 있다. 
MDS는 고차원의 데이터를 2차원 또는 3차원 공간에 매핑하여 데이터 간의 유사성과 차이를 시각화하는 방법이다. 주요 개념은 유사한 샘플은 그래프에서 가깝게, 유사하지 않은 샘플은 멀리 배치하는 것이다.

edgeR에서는 `plotMDS` 함수를 사용해서 수행할 수 있다.

```r
plotMDS(d, col=as.numeric(d$samples$group))
legend("bottomleft", as.character(unique(d$samples$group)), col=1:3, pch=20)
```

![image](https://github.com/user-attachments/assets/c8f46312-1eb4-42fc-85c5-33cb9453ddca)


그룹별로 색깔 라벨링을 해주었다. 플롯 상에서 같은그룹 내 샘플 간 거리는 가깝고, 그룹 간의 거리는 비교적 먼 것을 확인할 수 있다.

### 2.5.1 분산 추정 

분산 추정은 DGE 데이터 분석에서 중요한 단계이다. edgeR에서는 NB (Negative Binomial) 모델을 사용하여 각 유전자의 분산을 추정한다. 분산은 변이(다양성) 정도를 나타내는 지표이며 크게 두번의 분산 추정을 수행한다.

**공통 분산 추정 (Estimating the Common Dispersion)**

공통 분산은 전체 데이터셋에서의 전반적인 변이(다양성)을 나타낸다. 공통 분산을 추정함으로써 데이터셋 전체의 변이 정도를 파악할 수 있다.

```r
d1 <- estimateCommonDisp(d, verbose = TRUE)
```
```
Disp = 0.07469 , BCV = 0.2733
```

`verbose = TRUE`는 추정 과정을 자세히 출력해준다. `Disp`(Dispersion)는 추정된 공통 분산 값, `BCV`(Biological Coefficient of Variation)는 생물학적 변이 계수로, 분산의 제곱근이다.

**태그별 분산 추정 (Estimating Tagwise Dispersion)**

일반적으로 DE 분석에서는 경험적 베이지안 태그별 분산(empirical Bayes tagwise dispersions)을 사용한다. 반드시 공통 분산을 추정한 후에 태그별 분산을 추정해야 한다. 이때 태그는 유전자를 의미한다.

```r
d1 <- estimateTagwiseDisp(d1)
```

정리하면, 공통 분산 추정은 데이터셋 전체의 변이를 파악하는 것이며, 태그별 분산 추정은 각 유전자별로 변이 정도를 추정하는 것이다. 공통 분산만 계산할 경우 유전자마다 분산 특징, 즉 변화의 폭이 다르다는 점이 무시될 것이고, 유전자별 분산만 추정할 경우 데이터 수가 적으면 추정의 불확실성이 커질 수 있다. 경험적 베이지안 접근법은 데이터셋의 모든 유전자 정보를 결합하여 개별 유전자 분산 추정의 불확실성을 줄여준다.

edgeR에서는 두 분산추정을 `estimateDisp`로 한번에 수행할 수 있다.

```r
#d1 <- estimateCommonDisp(d, verbose = TRUE)
#d1 <- estimateTagwiseDisp(d1)
d1 <- estimateDisp(d1)
```

**cf) NB (Negative Binomial) 모델**

edgeR에서는 NB (Negative Binomial) 모델을 사용하여 각 유전자의 분산을 추정한다고 언급하였다. 이는 RNA-Seq으로 얻은 유전자의 발현량 분포가 음이항 분포를 따른다는 가정에 따른 것이다. 음이항 분포를 시각화하면 아래와 같은 분포를 보인다.

![image](https://github.com/user-attachments/assets/9262e248-f7f4-441d-9143-a1a85e63448b)


그리고 `mobData`의 `SL236` 샘플의 발현량을 시각화하면 아래와 같은 분포를 보인다.

![image](https://github.com/user-attachments/assets/7815516f-3cdc-490c-90a6-7102aa1ff442)


위 그림으로부터 알수있는 것은 무엇일까? 음이항 분포는 RNA-Seq 데이터의 두 가지 특성을 잘 포함한다. 첫번째는 과산포(Overdispersion) 처리이다. 포아송 분포는 평균과 분산이 동일하다는 가정을 가지고 있지만, RNA-Seq 데이터에서는 분산이 평균보다 훨씬 큰 경우가 많으며, 음이항 분포는 포아송 분포보다 RNA-Seq의 분포를 더 잘 반영한다.

두번째는 RNA-Seq 데이터의 비대칭성이다. 많은 유전자가 낮은 발현량을 가지며, 일부 유전자가 매우 높은 발현량을 가지는데, 포아송 분포는 대칭적인 분포를 가정하기 때문에 이런 특성을 잘 반영하지 못한다. 음이항 분포는 이러한 비대칭성을 반영할 수 있으므로 RNA-Seq 데이터의 유전자 발현량 형태를 모델링할 수 있다.

### 2.5.2 GLM 분산 추정

`estimateDisp` 함수는 공통 분산을 추정한 후 태그별 분산을 추정하는 함수이다. 공통 분산 추정 외에도, edgeR에서는 GLM(Generalized Linear Model)(일반화 선형 모델)을 사용하여 분산을 추정할 수 있다. 전자를 classic edgeR라고 부르며, 후자를 glm edgeR라고 부른다.

GLM을 사용한 분산 추정은 세 단계로 진행된다: 공통분산 추정, 추세분산(Trend Dispersion) 추정, 태그별 분산 추정. 고전적인 방법과 비교해보면 추세 분산 추정이 추가되었다.

```r
design <- model.matrix(~ 0 + d$samples$group)
colnames(design) <- levels(d$samples$group)

d2 <- estimateGLMCommonDisp(d, design)
d2 <- estimateGLMTrendedDisp(d2, design, method = "power")
d2 <- estimateGLMTagwiseDisp(d2, design)
```

**추세 분산 추정 (Estimating the Trend Dispersion)**

glm 분산 추정은 태그별 분산이 발현량의 함수로 변화하도록 모델링함으로써 데이터의 구조를 반영하여 분산을 조정한다. 예를 들어 발현량이 높은 유전자와 낮은 유전자의 분산이 다를 수 있다는 점을 고려한다. `method="power"`를 사용하면 발현량이 높을수록 분산이 증가하는 경향을 반영할 수 있다. 이는 고발현 유전자가 낮은 발현 유전자보다 더 큰 변이를 가질 수 있다는 점을 반영하는 효과가 있다.

edgeR에서는 세 분산추정을 `estimateDisp`로 한번에 수행할 수 있다.

```r
#d2 <- estimateGLMCommonDisp(d, design)
#d2 <- estimateGLMTrendedDisp(d2, design, method = "power")
#d2 <- estimateGLMTagwiseDisp(d2, design)
d2 <- estimateDisp(d2, design)
```

고전적인 분산 추정법에서도 `estimateDisp`를 사용한 것을 상기해보자. `design` 변수가 없으면 common 분산 추정이, design 변수를 넣어주면 glm 분산 추정이 수행된다.

```r
d1 <- estimateDisp(d1)
d2 <- estimateDisp(d2, design)
```

앞에서 BCV(생물학적 변이 계수)는 분산의 제곱근임을 언급하였다. d1과 d2의 BCV 플롯을 보면 두 분산 추정 방식의 차이를 확인할 수 있다.

```r
plotBCV(d1)
plotBCV(d2)
```
![image](https://github.com/user-attachments/assets/a6458255-657f-4445-8ea6-f2711207a329)
![image](https://github.com/user-attachments/assets/07a5cbe7-bd93-4895-bc5c-36d4c46b110a)


d1의 common 선을 보면 x축의 증가에 관계없이 일정하다. 즉, 발현량에 따른 분산 추세를 반영하지 않는다. 반면 d2의 trend 선을 보면 발현량에 따른 분산 추세를 반영하여, 발현량이 증가함에 따라 분산이 증가하는 경향을 보인다. 즉 발현량이 높은 태그는 더 높은 BCV 값을 가짐을 확인 가능하다.

즉, 모델 적합성 측면에서 봤을 때 common 분산을 기반으로 한 naive 모델은 데이터의 복잡성을 충분히 반영하지 못할 수 있으나 glm을 사용한 모델은 데이터의 다양한 변이성을 더 잘 반영하여, 데이터에 대한 적합성이 높다고 볼 수 있다.

### 2.7 차등 발현 분석

`d2`는 trended dispersion이 계산된 `DGEList` 객체이다. 우도비 테스트(likelihood ratio tests)나 준우도 F-테스트(quasi-likelihood F-tests)를 사용하여 차등 발현을 테스트할 수 있다. 아래 코드에서는 우도비 테스트를 사용하여 그룹 1,2 사이에 차등 발현을 테스트하였다.

```r
fit <- glmFit(d2, design)
lrt12 <- glmLRT(fit, contrast = c(1, -1, 0))
```

`glmFit` 함수는 분산 추정이 완료된 DGEList 객체 `d2`를 받아서 일반화 선형 모델(GLM)을 적합시킨다. `glmLRT` 함수는 GLM 적합 객체 `fit`에 대해 우도비 테스트를 수행한다. `contrast`로 비교할 그룹을 지정해줄 수 있는데 예를 들어 c(1, -1, 0)는 첫 번째 그룹(MM)과 두 번째 그룹(WM)을 비교한다.

테스트 결과는 `topTags` 함수를 통해 확인할 수 있다.

```r
topTags(lrt12, n = 10)
```
```
$table
A data.frame: 10 x 6
genes	logFC	logCPM	LR	PValue	FDR
<chr>	<dbl>	<dbl>	<dbl>	<dbl>	<dbl>
74	g74	-10.719067	9.509604	189.37491	4.352880e-43	1.235347e-39
1334	g1334	-9.904058	8.552973	136.72096	1.387788e-31	1.969271e-28
625	g625	7.417141	8.609845	115.51657	6.065021e-27	5.737510e-24
1111	g1111	-9.964418	8.754758	114.81804	8.626042e-27	6.120177e-24
1212	g1212	4.725747	9.527157	107.15658	4.113514e-25	2.334831e-22
1353	g1353	-9.341130	8.095157	105.28941	1.055387e-24	4.991979e-22
1208	g1208	5.366092	7.543455	102.13843	5.177444e-24	2.099084e-21
2468	g2468	-4.194420	8.455635	90.44836	1.898680e-21	6.735568e-19
2962	g2962	-6.253809	7.666776	87.83447	7.116772e-21	2.244155e-18
901	g901	-8.462521	7.303376	84.99636	2.989150e-20	8.483208e-18
$adjust.method'BH'
$comparison'1*MM -1*WM'
$test'glm'
```
테스트 결과 g74, g1334, g625 외 10개 유전자가 두 그룹 MM, WM 사이에 가장 유의미하게 차등발현된 것을 확인할 수 있다.

### 2.8 시각화 

차등 발현 데이터 `lrt12`를 사용해서 유의미하게 차등 발현된 유전자를 볼케이노 플롯으로 시각화할 수 있다.

```f
plotMD(lrt12)
abline(h=c(-1,1), col="blue")
```

![image](https://github.com/user-attachments/assets/f49b0520-35b6-4b4d-9827-f3dcc38be6bd)


유의미의 척도로는 일반적으로 p-값과 FDR, log2Fold Change(logFC)를 사용한다. FDR은 보정된 p-value로써, 다중 검정을 수행할 때 거짓 발견률을 제어하기 위해 사용된다. 프로브(유전자)의 개수가 많은 RNA-Seq 데이터에서는 다중 검정 보정이 필수적이므로, FDR을 주로 사용한다. 흔히 사용되는 FDR 임계값은 0.05이다.

log2Fold Change는 두 그룹 간의 발현 차이를 로그2 변환한 값이다. 위 코드에서 `abline(h = c(-1, 1), col = "blue")`는 로그2 폴드 변화가 ±1을 초과하는 유전자를 강조하는 기준선이 된다. 이는 두 배 이상의 발현 변화(2배 증가 또는 감소)를 의미한다.

## 마무리

본 포스팅에서 baySeq 데이터셋과 edgeR을 사용하여 DE 분석을 수행하였다. 라이브러리 크기를 고려한 데이터 정규화와 GLM 분산 추정을 수행하고, GLM 우도비 검정을 사용하여 차등 발현 분석을 수행하였다. 식별된 차등 발현 유전자셋은 이후 여러 분석에 사용될 수 있을 것이다.

정리된 전체 코드는 아래와 같다.

```r
# 패키지 호출
library("baySeq")
library("edgeR")

# 데이터 로드
data(mobData)
mobDataGroups <- c("MM", "MM", "WM", "WM", "WW", "WW")
d <- DGEList(counts=mobData, group=factor(mobDataGroups), genes=paste0("g", 1:3000))

# 유전자 필터링
non_zero_counts <- rowSums(d$counts) != 0
d <- d[non_zero_counts, ]

# 정규화
d <- calcNormFactors(d)

# 데이터 시각화
plotMDS(d, col=as.numeric(d$samples$group))
legend("bottomleft", as.character(unique(d$samples$group)), col=1:3, pch=20)

# GLM 분산 추정
design <- model.matrix(~ 0 + d$samples$group)
colnames(design) <- levels(d$samples$group)
d2 <- estimateDisp(d, design)

# GLM 우도비 검정
fit <- glmFit(d2, design)
lrt12 <- glmLRT(fit, contrast = c(1, -1, 0))
topTags(lrt12, n = 10)

# 결과 시각화
plotMD(lrt12)
abline(h=c(-1,1), col="blue")
```

## 참고자료

1. edgeR 공식 문서 [edgeR: differential analysis of sequence read count data](https://bioconductor.org/packages/release/bioc/vignettes/edgeR/inst/doc/edgeRUsersGuide.pdf)
2. [RNA Sequence Analysis in R: edgeR](https://web.stanford.edu/class/bios221/labs/rnaseq/lab_4_rnaseq.html)
