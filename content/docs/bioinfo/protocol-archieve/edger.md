# edgeR

## 들어가며

> 본 포스팅에서는 아래 내용을 다룬다.
> - edgeR, baySeq 설치
> - edgeR로 DE 분석

## 1. edgeR, baySeq 설치

아래 코드로 edgeR을 설치해준다. baySeq을 데이터셋으로 사용해줄 것이므로, baySeq도 같이 설치해준다.

```
> if (!require("BiocManager", quietly = TRUE))
    install.packages("BiocManager")
> BiocManager::install("edgeR")
> BiocManager::install("baySeq") 
```

설치가 완료되었다면 R 환경과 edgeR, baySeq 패키지의 버전을 확인해보자. 

```
> R.version
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

> packageVersion("edgeR")
[1] '3.32.1'

> packageVersion("baySeq")
[1] '2.24.0'
```

나의 경우 R(4.0.5), edgeR(3.32.1), baySeq(2.24.0)을 사용해주었다. 참고로 edgeR은 R(>= 3.6.0), baySeq은 R(>= 2.3.0) 버전을 필요로 한다.

## 2. DE analysis

### 2.1 데이터셋 가져오기

사용할 데이터셋을 로드해준다. 앞서 말했듯 baySeq의 `mobData`를 사용할 것이다.

```
> library("baySeq")
> library("edgeR")

> data(mobData)

> dim(mobData)
> head(mobData)
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

```
help(mobData)
```
![image](https://github.com/user-attachments/assets/3a6b0156-7d5c-4375-93d6-b38db901a21e)

확인 결과 6개의 샘플은 각각 3개의 그룹으로 나뉜다! `mobDataGroups`로 그룹을 지정해주고, 발현랑 데이터와 그룹 정보를 `d`로 넣어준다. 유전자 이름은 간단히 g1-g3000으로 넣어주었다.

```
> mobDataGroups <- c("MM", "MM", "WM", "WM", "WW", "WW")
> d <- DGEList(counts=mobData, group=factor(mobDataGroups), genes=paste0("g", 1:3000))
```

`d`는 다음과 같이 `counts`, `samples`, `genes` 정보가 담긴 `DGEList` 객체다. 

```
> head(d)

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

```
> non_zero_counts <- rowSums(d$counts) != 0
> d <- d[non_zero_counts, ]
> dim(d)

2838 6
```

필터링 결과 162개의 유전자가 제거되어 2,838개 유전자가 이후 분석에 사용되었다.

### 2.3 정규화

6개의 샘플은 라이브러리 크기가 모두 다르다. 라이브러리 크기는 다음 코드로 확인해볼수 있다.

```
> privious_lib_size <- d$samples$lib.size
> privious_lib_size

107684 217060 159645 142980 193570 205867
```

이러한 불균형 때문에 RNA-Seq 데이터의 정규화는 샘플 간의 차이를 비교할 수 있도록 하기 위해 매우 중요하다. 각 샘플의 라이브러리 크기를 고려해야 올바른 분석을 수행할 수 있다. 
edgeR에서는 이 차이를 정규화하기 위해 `calcNormFactors` 함수를 사용하여 라이브러리 크기에 대한 스케일링 팩터(비례 계수)를 계산하는데, 계산된 스케일링 팩터는 다음과 같다.

```
> d <- calcNormFactors(d)
> d$samples$norm.factors

0.9676656 0.9544311 0.8880339 0.8671801 1.2238652 1.1488350
```

이후 분석에서는 계산한 스케일링 펙터를 원래 라이브러리 크기에 곱한 값 `effective_lib_size`이 적용된다.

```
> effective_lib_size <- d$samples$lib.size * d$samples$norm.factors
> effective_lib_size

104202.1 207168.8 141770.2 123989.4 236903.6 236507.2
```

앞서 `d$samples$norm.factors`가 모두 1이었던 것을 다시 확인해보자! `calcNormFactors`를 수행하지 않으면 `d$samples$norm.factors`는 모두 1이다.

### 2.4 데이터 시각화

다차원 척도법(Multidimensional Scaling, MDS)을 사용하여 샘플 간 관계를 시각화할수 있다. 
MDS는 고차원의 데이터를 2차원 또는 3차원 공간에 매핑하여 데이터 간의 유사성과 차이를 시각화하는 방법이다. 주요 개념은 유사한 샘플은 그래프에서 가깝게, 유사하지 않은 샘플은 멀리 배치하는 것이다.

edgeR에서는 `plotMDS` 함수를 사용해서 수행할 수 있다.

```
> plotMDS(d, col=as.numeric(d$samples$group))
> legend("bottomleft", as.character(unique(d$samples$group)), col=1:3, pch=20)
```

![image](https://github.com/user-attachments/assets/ac62d000-1e57-4974-a45c-eb170ba0cd68)

그룹별로 색깔 라벨링을 해주었다. 플롯 상에서 같은그룹 내 샘플 간 거리는 가깝고, 그룹 간의 거리는 비교적 먼 것을 확인할 수 있다.

### 2.5 분산 추정 

분산 추정은 DGE 데이터 분석에서 중요한 단계이다! edgeR에서는 NB (Negative Binomial) 모델을 사용하여 각 유전자의 분산을 추정한다. 분산은 다양성 정도를 나타내는 지표이며 크게 두번의 분산 추정을 수행한다.

**공통 분산 추정 (Estimating the Common Dispersion)**

공통 분산은 전체 데이터셋에서의 전반적인 변이를 나타낸다. 공통 분산을 추정함으로써 데이터셋 전체의 변이 정도를 파악할 수 있다.


