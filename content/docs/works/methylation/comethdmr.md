+++
title = "[Tool] coMethDMR algorithm"
menu = "main"
+++

# coMethDMR algorithm

coMethDMR 분석 파이프라인의 워크플로우 정리.

## 1. 데이터 로드

사용된 데이터셋은 1~100만 bp 길이의 유전체상에 존재하는 1000개의 CpG 데이터이다. 총 10개의 샘플이 존재한다.

```python
methylation_data
```
```
	CpG1	CpG2	CpG3	CpG4	CpG5	CpG6	CpG7	CpG8	CpG9	CpG10	...	CpG991	CpG992	CpG993	CpG994	CpG995	CpG996	CpG997	CpG998	CpG999	CpG1000
0	0.936155	0.904764	0.374540	0.636410	0.081349	0.320050	0.437475	0.969914	0.408953	0.882660	...	0.503136	0.060511	0.212565	0.940459	0.734619	0.813795	0.815005	0.909320	0.772318	0.115091
1	0.036550	0.950675	0.168935	0.051824	0.315290	0.806561	0.507468	0.862128	0.294466	0.872869	...	0.600517	0.063321	0.194032	0.013094	0.765495	0.927752	0.847637	0.327497	0.867294	0.083217
2	0.689527	0.944471	0.758263	0.936212	0.927001	0.069424	0.476018	0.303275	0.647475	0.871836	...	0.951403	0.063554	0.209940	0.969912	0.763598	0.506142	0.848197	0.844783	0.137110	0.106012
3	0.462296	0.945843	0.956501	0.005230	0.239510	0.739904	0.558642	0.607905	0.440323	0.883899	...	0.335208	0.070964	0.227537	0.850616	0.771153	0.293489	0.849822	0.612940	0.010131	0.099719
4	0.822180	0.908646	0.340604	0.764528	0.371724	0.521460	0.562073	0.816779	0.646459	0.904089	...	0.388166	0.044486	0.209779	0.298052	0.731686	0.416566	0.828668	0.833650	0.932925	0.114219
5	0.692074	0.940921	0.672703	0.748384	0.929140	0.748184	0.667212	0.268562	0.483989	0.878665	...	0.214913	0.048223	0.206972	0.415775	0.730762	0.030026	0.836511	0.403601	0.563408	0.078829
6	0.354666	0.951978	0.141224	0.678500	0.577807	0.149249	0.094965	0.697833	0.728371	0.897234	...	0.023708	0.076149	0.220225	0.531572	0.748257	0.174647	0.841582	0.466299	0.779821	0.079064
7	0.692051	0.937909	0.849959	0.939569	0.811946	0.751279	0.097367	0.204242	0.521377	0.864589	...	0.932753	0.082971	0.234067	0.874307	0.737046	0.595366	0.809207	0.454184	0.802402	0.100416
8	0.437717	0.932510	0.123846	0.483860	0.368250	0.976618	0.370974	0.404969	0.455650	0.883551	...	0.309692	0.042409	0.192836	0.762212	0.757526	0.893304	0.832704	0.043100	0.692909	0.119894
9	0.676196	0.951263	0.243995	0.051166	0.630019	0.378316	0.087417	0.174785	0.188303	0.909332	...	0.243615	0.060495	0.209731	0.572770	0.775474	0.375821	0.804371	0.953307	0.825440	0.093325
10 rows × 1000 columns
```

1000개 CpG들의 위치와 서브영역(CpGi와의 관계 annotation)는 아래와 같다.

```python
cpg_positions
```
```
CpG1          904
CpG2         1533
CpG3         2176
CpG4         2929
CpG5         4522
            ...  
CpG996     998094
CpG997     998756
CpG998     998983
CpG999     999768
CpG1000    999991
Length: 1000, dtype: int64
```
```python
phenotype_data
```
```
CpG1       CpG island
CpG2            Shore
CpG3       CpG island
CpG4       CpG island
CpG5         Open Sea
              ...    
CpG996     CpG island
CpG997          Shore
CpG998          Shore
CpG999       Open Sea
CpG1000    CpG island
Length: 1000, dtype: object
```

## 2.  공동 메틸화 영역 식별

위에서 4가지 서브영역 정보가 `phenotype_data`에 존재했다. 유전체 영역은 CpGi와의 관계를 기반으로 정의될 수 있고 CpG island, Shore, Shelf, Open Sea 4가지 서브영역이 존재한다. 

각 서브영역에 대해서 최소 3개의 CpG(minCpGs = 3)를 포함하는 클러스터를 추출하고, 클러스터 내 두 개의 연속된 프로브 사이의 최대 간격(maxGap = 200)을 200bp로 설정한다. 
이 단계는 유전체 영역이 유사한 CpG 밀도를 갖도록 보장하는 데 도움을 준다.

```python
# 공동 메틸화된 CpG 클러스터 식별
max_gap = 200
min_cpgs = 3
clusters = []
current_cluster = [cpg_positions.index[0]]

for i in range(1, len(cpg_positions)):
    if cpg_positions.iloc[i] - cpg_positions.iloc[i-1] <= max_gap:
        current_cluster.append(cpg_positions.index[i])
    else:
        if len(current_cluster) >= min_cpgs:
            clusters.append(current_cluster)
        current_cluster = [cpg_positions.index[i]]

if len(current_cluster) >= min_cpgs:
    clusters.append(current_cluster)
```

선택된 클러스터들은 아래와 같다.

```python
clusters
```
```
[['CpG41', 'CpG42', 'CpG43'],
 ['CpG105', 'CpG106', 'CpG107'],
 ['CpG119', 'CpG120', 'CpG121'],
 ['CpG153', 'CpG154', 'CpG155'],
 ['CpG212', 'CpG213', 'CpG214'],
 ['CpG316', 'CpG317', 'CpG318', 'CpG319'],
 ['CpG376', 'CpG377', 'CpG378'],
 ['CpG406', 'CpG407', 'CpG408'],
 ['CpG420', 'CpG421', 'CpG422'],
 ['CpG438', 'CpG439', 'CpG440', 'CpG441'],
 ['CpG474', 'CpG475', 'CpG476', 'CpG477', 'CpG478', 'CpG479', 'CpG480'],
 ['CpG499', 'CpG500', 'CpG501'],
 ['CpG522', 'CpG523', 'CpG524'],
 ['CpG525', 'CpG526', 'CpG527'],
 ['CpG543', 'CpG544', 'CpG545', 'CpG546', 'CpG547'],
 ['CpG584', 'CpG585', 'CpG586'],
 ['CpG598', 'CpG599', 'CpG600'],
 ['CpG632', 'CpG633', 'CpG634'],
 ['CpG710', 'CpG711', 'CpG712'],
 ['CpG721', 'CpG722', 'CpG723'],
 ['CpG798', 'CpG799', 'CpG800'],
 ['CpG845', 'CpG846', 'CpG847'],
 ['CpG882', 'CpG883', 'CpG884'],
 ['CpG928', 'CpG929', 'CpG930'],
 ['CpG950', 'CpG951', 'CpG952'],
 ['CpG972', 'CpG973', 'CpG974']]
```

총 26개의 클러스터들이 선택되었다. 위 조건을 만족하는 클러스터에 한해, 공동 메틸화된 CpG 클러스터를 식별한다.

이를 위해 rdrop 통계를 사용하였다. rdrop 통계는 클러스터 내의 각 CpG 메틸화 수준과 다른 모든 CpG의 메틸화 수준 합계 간의 상관관계를 측정하며, rdrop 통계가 0.5보다 큰 CpG들이 선택되었다. 

```python
# rdrop 통계 계산 및 하위 지역 선택
selected_regions = {}
for cluster in clusters:
    rdrop_values = {}
    for cpg in cluster:
        others = [c for c in cluster if c != cpg]
        correlation, _ = pearsonr(methylation_data[cpg], methylation_data[others].sum(axis=1))
        rdrop_values[cpg] = correlation
    
    selected_cpgs = [cpg for cpg, rdrop in rdrop_values.items() if rdrop > 0.5]
    if selected_cpgs:
        selected_regions[tuple(selected_cpgs)] = rdrop_values
```

선택된 CpG 클러스터들은 아래와 같다. 

```python
selected_regions
```
```
{('CpG545',): {'CpG543': -0.20798349220151888,
  'CpG544': 0.09025095495141683,
  'CpG545': 0.5099911806540341,
  'CpG546': 0.03881238645267745,
  'CpG547': -0.14854059618834195},
 ('CpG585',): {'CpG584': -0.4008740503410496,
  'CpG585': 0.5248408261505223,
  'CpG586': 0.2767933217271546},
 ('CpG600',): {'CpG598': 0.2740304846847486,
  'CpG599': 0.28785032657529164,
  'CpG600': 0.6027281304631386},
 ('CpG711',): {'CpG710': -0.004012175545275608,
  'CpG711': 0.5035206449839701,
  'CpG712': 0.05672710552679505},
 ('CpG799',): {'CpG798': -0.17305750509188852,
  'CpG799': 0.5888170706161567,
  'CpG800': 0.14443457845442456},
 ('CpG950', 'CpG951'): {'CpG950': 0.5790140025165356,
  'CpG951': 0.5058922146325839,
  'CpG952': 0.21105056884644954}}
```

6개의 클러스터들이 공동 메틸화된 클러스터로 나타났다. 아래 클러스터를 예로 들어 설명해보자면

```
 ('CpG950', 'CpG951'): {'CpG950': 0.5790140025165356,
  'CpG951': 0.5058922146325839,
  'CpG952': 0.21105056884644954}
```

이 클러스터에는 세 개의 CpG (CpG950, CpG951, CpG952)가 포함되어 있으며 CpG950과 CpG951이 공동 메틸화된 CpG로 선택되었다(각각 rdrop 값 = 0.5790, 0.5058). CpG950의 rdrop을 계산할땐 다른 CpG들의 메틸화 수준 합계, 즉 CpG951과 CpG952의 메틸화 수준을 합산하고 CpG950의 메틸화 수준과 계산한 합계 사이의 상관관계를 계산하여 rdrop 통계를 얻는다. 

```python
# 각 샘플에서 CpG950의 메틸화 수준
methylation_data['CpG950']
```
```
0    0.593624
1    0.573490
2    0.594965
3    0.578348
4    0.580918
5    0.589114
6    0.557627
7    0.589409
8    0.587639
9    0.572202
Name: CpG950, dtype: float64
```
```python
# 각 샘플에서 CpG951, CpG952의 메틸화 수준 합계
methylation_data[['CpG951', 'CpG952']].sum(axis=1)
```
```
0    1.284285
1    0.661566
2    0.867348
3    0.637122
4    0.325680
5    1.016560
6    0.308581
7    1.274586
8    0.313344
9    0.846620
dtype: float64
```
이 두 벡터 간의 상관관계를 계산한 결과가 0.5790라면, 이는 CpG545의 메틸화 수준이 클러스터 내 다른 CpG들의 메틸화 수준 합계와 상당한 상관관계를 가진다는 의미이고 이 상관관계가 0.5 이상이기 때문에 CpG545는 공동 메틸화된 CpG로 선택되었다.

## 3. 공동 메틸화된 CpG와 표현형 간 연관성 테스트

두 번째 단계에서는 식별한 6개 CpG 클러스터들과 표현형 간의 연관성을 테스트한다. 앞서 10개의 샘플이 존재했으며 해당 샘플의 표현형은 다음과 같다. 

```python
phenotype_samples
```
```
0    2
1    1
2    2
3    2
4    1
5    1
6    0
7    0
8    1
9    2
Name: Phenotype, dtype: int64
```

`selected_regions` 내 각 클러스터의 rdrop 값과 `phenotype_samples` 데이터를 사용해서 혼합 효과 모델을 만들고 생성한 모델로 클러스터와 표현형 간의 연관성 테스트를 수행한다. 

```python
# 5. 각 클러스터와 표현형 간의 연관성 테스트 (혼합 효과 모델)
results = {}
for region, rdrop_values in selected_regions.items():
    region_data = methylation_data[list(region)]
    region_mean = region_data.mean(axis=1)

    # 혼합 효과 모델을 위한 설계 행렬 생성
    exog = sm.add_constant(phenotype_samples)
    
    # 혼합 효과 모델 실행
    model = MixedLM(region_mean, exog, groups=phenotype_samples.index)
    result = model.fit()
    results[region] = result.pvalues
```
```python
for region, pvalues in results.items():
    print(f"Region: {region}")
    print(f"P-values:\n{pvalues}\n")
```
```
Region: ('CpG545',)
P-values:
const         0.000000e+00
Phenotype    3.397325e-142
Group Var              NaN
dtype: float64

Region: ('CpG585',)
P-values:
const        0.000000
Phenotype    0.920965
Group Var         NaN
dtype: float64

Region: ('CpG600',)
P-values:
const        9.114306e-80
Phenotype    5.506261e-01
Group Var             NaN
dtype: float64

Region: ('CpG711',)
P-values:
const        0.0000
Phenotype    0.0083
Group Var       NaN
dtype: float64

Region: ('CpG799',)
P-values:
const        0.001092
Phenotype    0.768312
Group Var         NaN
dtype: float64

Region: ('CpG950', 'CpG951')
P-values:
const        0.000061
Phenotype    0.539738
Group Var         NaN
dtype: float64
```

수행 결과 두개의 CpG 클러스터 ('CpG545',)와 ('CpG711',)가 유의미한 것으로 나왔다 (P-Value 각각 3.397325e-142, 0.0083). 이는 이 클러스터들의 메틸화 수준이 표현형과 매우 강하게 연관되어 있으며 따라서 질병이 이 클러스터들의 메틸화 수준에 유의미한 영향을 미쳤을것이라는 추론이 가능하다. 

추가로, Group Var은 혼합 효과 모델에서 그룹 간의 변동성에 대한 p-value인데 모두 NaN으로 나타난다. 이는 그룹 간 변동성이 모델에 의해 추정되지 않았거나 충분한 데이터가 없어서 계산되지 않았음을 의미한다. 

```python
selected_cpgs = ['CpG545', 'CpG585', 'CpG600', 'CpG711', 'CpG799', 'CpG950', 'CpG951']
methylation_data[selected_cpgs]
```
```
	CpG545	CpG585	CpG600	CpG711	CpG799	CpG950	CpG951
0	0.939047	0.853965	0.079602	0.037389	0.147073	0.593624	0.986211
1	0.920067	0.860595	0.075892	0.068256	0.779845	0.573490	0.350125
2	0.930277	0.825717	0.112845	0.038250	0.688135	0.594965	0.549530
3	0.953499	0.846528	0.109850	0.034815	0.306313	0.578348	0.338861
4	0.943552	0.816785	0.109895	0.048914	0.838997	0.580918	0.037571
5	0.943878	0.831356	0.108046	0.063393	0.195167	0.589114	0.737426
6	0.954683	0.853414	0.105699	0.027360	0.582714	0.557627	0.027852
7	0.956040	0.829398	0.096079	0.069354	0.000598	0.589409	0.982740
8	0.957331	0.840570	0.104343	0.061956	0.576489	0.587639	0.027107
9	0.916084	0.836204	0.074147	0.031265	0.049860	0.572202	0.533055
```
찍어보니 그룹 간 변동성이 좀 적어보이긴 한다..!

## 마무리

coMethDMR 분석 파이프라인의 워크플로우를 파이썬으로 간단히 수행해보았다. 

분석 결과인 `results` 딕셔너리는 key가 CpG 클러스터를 의미하기때문에 길이가 2 이상이어야 하긴 하지만, 해당 부분만 제외하면 제시된 코드로 coMethDMR의 알고리즘을 이해하고 DMR 분석 결과를 도출할수있다. 

전체 분석 코드는 아래와 같다.

```python
import numpy as np
import pandas as pd
from scipy.stats import pearsonr
import statsmodels.api as sm
from statsmodels.regression.mixed_linear_model import MixedLM

# 10개의 샘플과 1000개의 CpG를 갖는 임의의 메틸화 데이터 생성
np.random.seed(42)
num_cpgs = 1000
num_samples = 10

# 60%의 CpG는 완전히 랜덤하게 생성
random_cpgs = int(num_cpgs * 0.6)
random_data = pd.DataFrame(
    np.random.rand(num_samples, random_cpgs),
    columns=[f'CpG{i}' for i in range(1, random_cpgs + 1)]
)

# 나머지 40%의 CpG에 대해서는 값들이 비슷하게 설정
similar_cpgs = num_cpgs - random_cpgs
similar_data = {}

for i in range(random_cpgs + 1, num_cpgs + 1):
    mean_value = np.random.rand()  # 임의의 평균값
    small_variation = np.random.rand(num_samples) * 0.05  # 작은 범위 내의 변동
    similar_values = mean_value + small_variation
    similar_data[f'CpG{i}'] = similar_values

# similar_data 딕셔너리를 DataFrame으로 변환
similar_data_df = pd.DataFrame(similar_data)

# 랜덤 데이터와 비슷한 값의 데이터를 결합
methylation_data = pd.concat([random_data, similar_data_df], axis=1)

# 컬럼을 무작위로 섞기
shuffled_columns = np.random.permutation(methylation_data.columns)
methylation_data = methylation_data[shuffled_columns]
methylation_data.columns = [f'CpG{i}' for i in range(1, len(methylation_data.columns) + 1)] 

# 1000개의 CpG에 대한 임의의 위치 생성
cpg_positions = pd.Series(np.sort(np.random.randint(1, 1000000, size=1000)), index=methylation_data.columns)

# phenotype_categories를 1000개의 CpG에 할당
phenotype_categories = ['CpG island', 'Shore', 'Shelf', 'Open Sea']
phenotype_data = pd.Series(np.random.choice(phenotype_categories, size=1000), index=methylation_data.columns)

# 샘플별 표현형 데이터 생성 (예: 질병 단계)
phenotype_values = np.random.randint(0, 3, size=num_samples)  # 0, 1, 2 세 가지 질병 단계로 표현
phenotype_samples = pd.Series(phenotype_values, index=methylation_data.index, name='Phenotype')

# 공동 메틸화된 CpG 클러스터 식별
max_gap = 200
min_cpgs = 3
clusters = []
current_cluster = [cpg_positions.index[0]]

for i in range(1, len(cpg_positions)):
    if cpg_positions.iloc[i] - cpg_positions.iloc[i-1] <= max_gap:
        current_cluster.append(cpg_positions.index[i])
    else:
        if len(current_cluster) >= min_cpgs:
            clusters.append(current_cluster)
        current_cluster = [cpg_positions.index[i]]

if len(current_cluster) >= min_cpgs:
    clusters.append(current_cluster)

# rdrop 통계 계산 및 하위 지역 선택
selected_regions = {}
for cluster in clusters:
    rdrop_values = {}
    for cpg in cluster:
        others = [c for c in cluster if c != cpg]
        correlation, _ = pearsonr(methylation_data[cpg], methylation_data[others].sum(axis=1))
        rdrop_values[cpg] = correlation
    
    selected_cpgs = [cpg for cpg, rdrop in rdrop_values.items() if rdrop > 0.5]
    if selected_cpgs:
        selected_regions[tuple(selected_cpgs)] = rdrop_values

# 5. 각 클러스터와 표현형 간의 연관성 테스트 (혼합 효과 모델)
results = {}
for region, rdrop_values in selected_regions.items():
    region_data = methylation_data[list(region)]
    region_mean = region_data.mean(axis=1)

    # 혼합 효과 모델을 위한 설계 행렬 생성
    exog = sm.add_constant(phenotype_samples)
    
    # 혼합 효과 모델 실행
    model = MixedLM(region_mean, exog, groups=phenotype_samples.index)
    result = model.fit()
    
    # 결과 저장
    results[region] = result.pvalues
```

