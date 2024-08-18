+++
title = "[Runtime] 런타임 성능 예측하기"
menu = "main"
+++
구축한 프로토타입의 성능을 값이 각각 100개, 1000개, 10000개인 작은 데이터세트에 대해서 테스트했고, 결과는 다음과 같았다. 

```python
original_table()
```
```
       N	  Actual	
     100	   0.063	
   1,000	   0.565	
  10,000	   5.946	
<algs.table.DataTable at 0x7f3e5ca4c730>
```

위 테스트결과를 사용해서 더큰 문제 인스턴스(값이 100,000개나 1,000,000개)에서 이 프로토타입의 성능을 예측하고싶다고 해보자. N을 넣어주면 예상시간을 출력하는 함수 T(N)을 찾으면된다. 데이터에 대한 모델로는 선형, 2차, NlogN 함수를 사용할것이다.

```python
import math

try:
    import numpy as np
    from scipy.optimize import curve_fit
    from scipy.stats.stats import pearsonr
    from scipy.special import factorial

    def linear_model(n, a, b):
        """Formula for A*N + B linear model with two coefficients."""
        return a*n + b

    def n_log_n_model(n, a):
        """Formula for A*N*Log_2(N) with single coefficient."""
        return a*n*np.log2(n)

    def quadratic_model(n, a, b):
        """Formula for A*N*N + B*N quadratic model with three coefficients."""
        return a*n*n + b*n

except ImportError:
    if numpy_error == []:
        print('trying to continue without numpy or scipy')
    numpy_error.append(1)

    def linear_model(n, a, b):
        """Formula for A*N + B linear model with two coefficients."""
        return a*n + b

    def n_log_n_model(n, a):
        """Formula for A*N*Log_2(N) with single coefficient."""
        return a*n*math.log2(n)

    def quadratic_model(n, a, b):
        """Formula for A*N*N + B*N quadratic model with three coefficients."""
        return a*n*n + b*n
```

먼저 N=100, 1000, 10000개인 데이터세트에 대해 curve_fit을 사용하여 실제 데이터(nvals, yvals)에 대해 세 가지 모델(선형, 2차, N log N)을 피팅한다. 다음으로 N=100,000, 1,000,000, 10,000,000에 대해 T(N)으로 런타임성능 추정치를 계산한다. 

```python
def prototype_table(output=True, decimals=3):
    """
    Generate table of results for prototype application.

    The prototype application is simply a request to sort the N values.
    """
    trials = [100, 1000, 10000]
    nvals = []
    yvals = []
    for n in trials:
        sort_time = 1000*min(timeit.repeat(stmt='x.sort()', setup='''
        import random
        x=list(range({}))
        random.shuffle(x)'''.format(n), repeat=100, number=100))
        nvals.append(n)
        yvals.append(sort_time)

    def quad_model(n, a, b):
        if a < 0:     # attempt to PREVENT negative coefficient.
            return 1e10
        return a*n*n + b*n
    # Coefficients are returned as first argument
    if numpy_error:
        nlog_n_coeffs = linear_coeffs = quadratic_coeffs = [0,0]
    else:
        import numpy as np
        from scipy.optimize import curve_fit
        [nlog_n_coeffs, _] = curve_fit(n_log_n_model, np.array(nvals), np.array(yvals))
        [linear_coeffs, _] = curve_fit(linear_model, np.array(nvals), np.array(yvals))
        [quadratic_coeffs, _] = curve_fit(quad_model, np.array(nvals), np.array(yvals))

    if output:
        print('Linear    = {:f}*N + {:f}'.format(linear_coeffs[0], linear_coeffs[1]))
        print('Quadratic = {}*N*N + {}*N'.format(quadratic_coeffs[0], quadratic_coeffs[1]))
        print('N Log N   = {:.12f}*N*log2(N)'.format(nlog_n_coeffs[0]))
        print()

    tbl = DataTable([12,10,10,10,10],['N','Time','Linear','Quad','NLogN'],
                    output=output, decimals=decimals)

    for n,p in zip(nvals,yvals):
        tbl.row([n, p,
            linear_model(n, linear_coeffs[0], linear_coeffs[1]),
            quadratic_model(n, quadratic_coeffs[0], quadratic_coeffs[1]),
            n_log_n_model(n, nlog_n_coeffs[0])])

    for n in [100000, 1000000, 10000000]:
        sort_time = 1000*min(timeit.repeat(stmt='x.sort()', setup='''
                    import random
                    x=list(range({}))
                    random.shuffle(x)'''.format(n), repeat=100, number=100))
        tbl.row([n, sort_time,
            linear_model(n, linear_coeffs[0], linear_coeffs[1]),
            quadratic_model(n, quadratic_coeffs[0], quadratic_coeffs[1]),
            n_log_n_model(n, nlog_n_coeffs[0])])

    if output:
        print('Linear', tbl.pearsonr('Time', 'Linear'))
        print('Quad', tbl.pearsonr('Time', 'Quad'))
        print('NLogN', tbl.pearsonr('Time', 'NLogN'))
        print(tbl.best_model('Time'))
    return tbl
```

timeit.repeat는 2개의 변수 stmt, setup을 받는다. stmt로는 실행하려는 함수 x.sort()이, setup으로는 "x=list(range(n) random.shuffle(x)"이 할당된다. 
이 함수를 100번 실행한다. 이를 100번 반복한 후 최소 실행시간을 밀리초(ms) 단위로 변환해서 sort_time에 할당한다.

```python
sort_time = 1000*min(timeit.repeat(stmt='x.sort()', setup='''
            import random
            x=list(range({}))
            random.shuffle(x)'''.format(n), repeat=100, number=100))
```
            
수행 결과는 다음과 같다. 

```python
prototype_table()
```
```
Linear    = 0.000396*N + -0.012965
Quadratic = 2.75896277853117e-09*N*N + 0.00036707888835004306*N
N Log N   = 0.000029743811*N*log2(N)

           N	      Time	    Linear	      Quad	     NLogN	
         100	     0.039	     0.027	     0.037	     0.020	
       1,000	     0.370	     0.383	     0.370	     0.296	
      10,000	     3.947	     3.945	     3.947	     3.952	
     100,000	    48.929	    39.572	    64.298	    49.403	
   1,000,000	   768.324	   395.832	  3126.042	   592.841	
  10,000,000	 10671.521	  3958.434	279567.067	  6916.476	

Linear PearsonRResult(statistic=0.9996273887127448, pvalue=2.0823289056364035e-07)
Quad PearsonRResult(statistic=0.9982299651077396, pvalue=4.696762499253912e-06)
NLogN PearsonRResult(statistic=0.9999104564904093, pvalue=1.2026701182996603e-08)

[(4, 0.999999971772936, 1.2235089328790567, 8.982099164704104e-05, -0.00102150241110649), (5, 0.9999973567086468, 10.205225823563975, 3.350842762257358e-11, 0.0007320702915418002), (3, 0.9999104564904094, 60.573594846215904, 4.583771446952524e-05), (2, 0.9996273887127447, 107.15215916137456, 0.0010717470461801216, -69.19330278779357)]
```

선형 모델은 입력 크기 N에 대해 선형적인 시간 복잡도를 가정한다. `T(N) = 0.000396*N + -0.012965`로 계산되었다. Quadratic 모델(2차 모델)은 입력 크기 N에 대해 2차 시간 복잡도를 가정하며 `T(N) = 2.75896277853117e-09*N*N + 0.00036707888835004306*N` 으로 계산되었다. N Log N 모델은 입력 크기 N에 대해 N log N 시간 복잡도를 가정하며 `T(N) = = 0.000029743811 * N * log2(N)`으로 계산되었다. 

실제 수행시간 Time과 계산된 수행시간 Linear, Quad, NLogN을 피어슨 상관계수를 계산해서 분석하였다. 
`Linear PearsonRResult(statistic=0.9996273887127448, pvalue=2.0823289056364035e-07)`에서 상관계수(statistic) = 0.9996로 매우 높고 선형 모델이 실제 데이터와 거의 일치하는 것을 의미한다. 
p-value= 2.0823289056364035e-07로 매우 작고 이는 상관관계가 통계적으로 유의미함을 의미한다.  Quad PearsonRResult, NLogN PearsonRResult의 statistic은 각각 0.9982, 0.9999로 매우 높고 각 모델이 실제 데이터와 거의 일치함을 보였다. Linear, Quad, NLogN중 가장 실제 데이터와 일치하는 모델은 NLogN으로 볼수있다.

실제로 Linear, Quad는 문제 크기가 증가함에 따라 런타임 성능을 실제보다 과소/과대평가하며 NLogN의 일치도가 가장 높다. 위 결과의 N = 100,000을 확인해보면 일반적인 경향성과 일치하는 결과가 나왔음을 확인할수있다. 




