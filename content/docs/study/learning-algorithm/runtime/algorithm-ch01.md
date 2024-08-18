+++
title = "max() 수행시간 측정하기"
menu = "main"
+++

# max() 수행시간 측정하기

## 1. 두종류의 인스턴스에 max() 실행

```python
def run_init_trial(output=True):
    """First Table in chapter 1."""
    n = 100
    tbl = DataTable([12, 12, 12], ['N', 'Ascending', 'Descending'], output=output, decimals=3)

    while n <= 1000000:
        m_up = measure_time(n, order='ascending')
        m_down = measure_time(n, order='descending')

        tbl.row([n, m_up, m_down])
        n *= 10
    return tbl
```

오름차순 정수, 내림차순 정수 리스트가 있다. 두 문제 인스턴스에 max()를 수행한 시간 결과를 위 코드로 확인할수있다. 

while문을 통해 오름차순 리스트와 내림차순 리스트에서 native_largest 함수의 실행 시간을 측정하여 m_up, m_down으로 받고 tbl에 저장한다. 리스트의 길이 n을 10배씩 증가시켜가며 n= 1000000까지 수행한다. 

```python
run_init_trial()
```
```
           N	   Ascending	  Descending	
         100	       0.002	       0.002	
       1,000	       0.015	       0.013	
      10,000	       0.119	       0.124	
     100,000	       1.164	       1.241	
   1,000,000	      11.834	      12.615	
<algs.table.DataTable at 0x7f90394aac70>
```

수행해보니 N이 충분히 크다면(1000 이상) max()의 수행시간은 오름차순 리스트일때가 더 느리다. 그리고 N이 10배 증가하면 max()의 수행시간도 10배 증가하는 경향이 있다. 

cf) measure_time() 함수

```python
def measure_time(n, order):
    """Measure the execution time of native_largest function based on the order."""
    if order == 'ascending':
        setup_code = f'up = list(range(1, {n} + 1))'
        stmt_code = 'native_largest(up)'
    elif order == 'descending':
        setup_code = f'down = list(range({n}, 0, -1))'
        stmt_code = 'native_largest(down)'
    else:
        raise ValueError("Invalid order specified. Use 'ascending' or 'descending'.")

    exec_time = 1000 * min(timeit.repeat(stmt=stmt_code, setup=f'''
from ch01.largest import native_largest
{setup_code}''', repeat=10, number=50)) / 50

    return exec_time

def native_largest(A):
    """Simply access built-in max() method."""
    return max(A)
```

measure_time(1000, order=ascending)을 수행하면 setup_code = "up=list(range(1, 1001))", stmt_code = 'native_largest(up)'가 할당되고 exec_time이 계산된다.

```python
exec_time = 1000 * min(timeit.repeat(
    stmt='native_largest(up)', 
    setup=f'''
from ch01.largest import native_largest
up = list(range(1, 1001))''', 
    repeat=10, 
    number=50
)) / 50
```

timeit.repeat는 2개의 변수 stmt, setup을 받는다. exec_time 계산 코드를 자세히보면 stmt로는 실행하려는 함수 max(up)이, setup으로는 아래 내용이 할당된다. 
즉 아래 설정 하에서 up 리스트에 대한 max() 함수를 10번 실행하고 최소 실행 시간을 선택한다. 이를 50번 실행한 후 각 최소 실행 시간을 밀리초(ms) 단위로 변환하고 그 평균값을 exec_time에 할당한다. 

```python
from ch01.largest import native_largest
up = list(range(1, 1001))
```

## 2. 최댓값 찾기 알고리즘 alternate()

```python
def alternate(A):
    for v in A:
        v_is_largest = True
        for x in A:
            if v < x:
                v_is_largest = False
                break
        if v_is_largest:
            return v
    return None
```

alternate(A)는 리스트 A 안의 모든값에 대해서 다른값보다 큰값이 있는지를 확인하여 v_is_largest가 True가 될때를 찾는다(if v_is_largest: return v).

A = [int(x) for x in list("152934")] 를 넣어줬을때 alternate(A) 알고리즘을 시각화하면 다음과 같다.

```python
visualize_alternate(A)
```
```
	  1        5	    2	    9	    3	    4

1	1 < 1?	5 < 1?	2 < 1?	9 < 1?	
5	1 < 5?	5 < 5?	2 < 5?	9 < 5?	
2	        5 < 2?		    9 < 2?	
9	        5 < 9?		    9 < 9?	
3				            9 < 3?	
4				            9 < 4?	
```

결과를 보면 이 문제 인스턴스에서는 미만 연산이 14회 수행되었다. 그렇다면 위 알고리즘은 오름차순과 내림차순 리스트중 어느 경우가 최상/최악의 문제 인스턴스가 될까?

```python
visualize_alternate([9,5,4,3,2,1])
visualize_alternate([1,2,3,4,5,9])
```
```
	  9	        5        4	    3	    2	    1

9	9 < 9?	
5	9 < 5?	
4	9 < 4?	
3	9 < 3?	
2	9 < 2?	
1	9 < 1?

	  1	        2	    3	    4	    5	    9

1	1 < 1?	2 < 1?	3 < 1?	4 < 1?	5 < 1?	9 < 1?	
2	1 < 2?	2 < 2?	3 < 2?	4 < 2?	5 < 2?	9 < 2?	
3		    2 < 3?	3 < 3?	4 < 3?	5 < 3?	9 < 3?	
4			        3 < 4?	4 < 4?	5 < 4?	9 < 4?	
5				            4 < 5?	5 < 5?	9 < 5?	
9					                5 < 9?	9 < 9?	


```

내림차순 리스트에서 미만연산이 6회, 오름차순 리스트에서 26회 수행되었다. 내림차순 리스트가 최상의 인스턴스, 오름차순이 최악의 문제 인스턴스임을 알수있다. 이는 max() 함수에서 나타난 경향성과 일치한다.

##  3. largest()와 alternate() 성능 비교하기

largest()는 다음과 같이 동작해서 최댓값을 찾는 또다른 알고리즘이다. 

```python
def largest(A):
    my_max = A[0]
    for idx in range(1, len(A)):
	if my_max < A[idx]:
	    my_max = A[idx]
	return my_max
```

최악의 문제 인스턴스로 max()와 alternate()의 런타임 성능을 비교해본다. 오름차순 문제 인스턴스에 max(), alternate()를 수행한 시간 결과를 아래 코드로 확인할수있다. 

while문을 통해 오름차순 리스트에서 native_largest 함수의 실행 시간을 측정하여 m_up, m_down으로 받고 tbl에 저장한다. 리스트의 길이 n을 2배씩 증가시켜가며 n= 2048까지 수행한다. 

```python
def run_largest_alternate(output=True, decimals=3):
    """Generate tables for largest and alternate."""
    n = 8
    tbl = DataTable([8,10,15,10,10],
                   ['N', '#Less', '#LessA', 'largest', 'alternate'],
                   output=output, decimals=decimals)
    tbl.format('#Less', ',d')
    tbl.format('#LessA', ',d')

    while n <= 2048:
        ascending = list(range(n))

        largest_up = 1000*min(timeit.repeat(stmt='largest_func({})'.format(ascending),
            setup='from ch01.largest import largest as largest_func', repeat=10, number=50))/50
        alternate_up = 1000*min(timeit.repeat(stmt='alternate_func({})'.format(ascending),
            setup='from ch01.largest import alternate as alternate_func', repeat=10, number=50))/50

        up_count = [RecordedItem(i) for i in range(n)]
        RecordedItem.clear()
        largest_func(up_count)
        largest_counts = RecordedItem.report()
        RecordedItem.clear()

        up_count = [RecordedItem(i) for i in range(n)]
        RecordedItem.clear()
        alternate_func(up_count)
        alternate_counts = RecordedItem.report()
        RecordedItem.clear()

        tbl.row([n, sum(largest_counts), sum(alternate_counts), largest_up, alternate_up])

        n *= 2

    if output:
        print()
        print('largest', tbl.best_model('largest', Model.LINEAR))
        print('Alternate', tbl.best_model('alternate', Model.QUADRATIC))
    return tbl
```

stmt로는 실행하려는 함수 'largest_func(ascending)'와 'alternate_func(ascending)'이, setup으로는 아래 내용이 할당된다. 
즉 아래 설정 하에서 ascending 리스트에 대한 max()와 alternate() 함수를 10번 실행하고 최소 실행 시간을 선택한다. 이를 50번 실행한 후 각 최소 실행 시간을 밀리초(ms) 단위로 변환하고 그 평균값을 largest_up, alternate_up에 할당한다. 

```python
from ch01.largest import largest as largest_func
from ch01.largest import alternate as alternate_func
```

실행 결과는 다음과 같다. 

```python
run_largest_alternate()
```
```
       N	     #Less	         #LessA	   largest	 alternate	
       8	         7	             43	     0.002	     0.003	
      16	        15	            151	     0.003	     0.004	
      32	        31	            559	     0.002	     0.012	
      64	        63	          2,143	     0.003	     0.045	
     128	       127	          8,383	     0.007	     0.163	
     256	       255	         33,151	     0.013	     0.634	
     512	       511	        131,839	     0.028	     2.504	
   1,024	     1,023	        525,823	     0.058	     9.848	
   2,048	     2,047	      2,100,223	     0.118	    39.925	

largest [(2, 0.9994818643879974, 0.0011859006277159794, 5.7159479891483e-05, -4.349751419673701e-05)]
Alternate [(5, 0.9999967899709289, 0.033318429195860845, 9.57628373931414e-06, -0.0001234652060780368)]
<algs.table.DataTable at 0x7f9039533280>
```

수행해보니 문제 인스턴스가 두배로 증가하면 alternate()의 미만연산 횟수는 4배로 증가해서 largest()의 횟수를 훨씬능가한다. 3, 4번째 컬럼은 수행 시간을 나타내는데, 마찬가지 alternative() 수행완료시간이 4배 증가하였다.

n = 16,384로 설정한 후 한번더 실행해보았다. 

```python
run_largest_alternate()
```
```
   4,096	     4,095	      8,394,751	     0.234	   156.370	
   8,192	     8,191	     33,566,719	     0.472	   626.196	
  16,384	    16,383	    134,242,303	     0.950	  2501.077
```

위 표를 통해 문제 인스턴스의 크기가 증가함에 따라 수행 시간을 예측할수있다. largest()는 N이 2배가될때 수행 시간도 2배가 됨을 예측할수 있다. 

