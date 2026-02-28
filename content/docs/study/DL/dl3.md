---
date : 2026-02-27
tags: ['2026-02']
categories: ['DL']
bookHidden: true
title: "Conv1D 기반 DNA 분석 #3 크로마틴 접근성 추가"
---

# Conv1D 기반 DNA 분석 #3 크로마틴 접근성 추가

#2026-02-27

---

이전 모델에서 우리는 DNA 서열 101글자만 보고 JUND가 붙을지 말지를 예측했고 꽤 잘 작동했다. 그런데 생물학의 현실은 좀 더 복잡한데 JUND가 좋아하는 서열 패턴(TGACTCA 같은 모티프)이 거기 있어도, 그 DNA 구간이 물리적으로 접근 불가능한 상태라면 JUND는 절대 결합할 수 없다. 


```plain text
열린 크로마틴 (Open):   ====○====  ← TF 접근 가능, 결합 가능성 높음
닫힌 크로마틴 (Closed): ████████  ← TF 접근 불가, 결합 가능성 낮음
```

우리 세포 안의 DNA는 그냥 풀어져서 떠다니는 게 아니라 히스톤이라는 작은 단백질 뭉치에 실타래처럼 감겨 있고 이렇게 DNA가 히스톤에 감긴 구조를 크로마틴이라고 부른다.

DNA가 히스톤에 빽빽하게 감긴 구간에서는 전사인자가 DNA에 접근할 수 없고, 느슨하게 풀린 구간에서는 전사인자가 자유롭게 달라붙을 수 있다. 생물학자들은 이 "얼마나 풀려 있는가"를 실험으로 측정할 수 있다. ATAC-seq 같은 기술을 쓰면 게놈의 각 구간이 얼마나 열려 있는지를 숫자로 얻을 수 있고 이 숫자가 바로 크로마틴 접근성이다. 숫자가 크면 활짝 열려 있는 거고, 작으면 꽉 닫혀 있는 거다.

```plain text
# accessibility.txt 파일:
chr22:20208963-20209064    0.003902
chr22:29673572-29673673    0.004378
...
```

accessibility.txt 파일을 보면 chr22:20208963-20209064에 대해 0.003902 같은 값이 적혀 있다. 이건 "22번 염색체의 이 구간은 접근성이 0.003902다"라는 뜻이다. 각 DNA 조각마다 이 숫자가 하나씩 딸려 있는 것이다.


###

#1 모델 구조: 두 개의 입력

```python
features = tf.keras.Input(shape=(101, 4))       # DNA 서열
accessibility = tf.keras.Input(shape=(1,))       # 크로마틴 접근성 스칼라
```

이전 모델의 입력은 DNA 서열 하나뿐이었다. 이제는 두 가지 정보를 동시에 모델에 넣어야 하는데 DNA 서열 (101, 4) 행렬과, 크로마틴 접근성이라는 숫자 하나이다.


```python
for i in range(3):
    prev = layers.Conv1D(filters=15, kernel_size=10, activation=tf.nn.relu, padding='same')(prev)
    prev = layers.Dropout(rate=0.5)(prev)
```

DNA 서열 쪽 처리는 이전과 완전히 동일하다. 세 겹의 Conv1D가 서열을 훑으면서 모티프 패턴을 추출하고, 각 레이어 뒤에 Dropout이 과적합을 방지한다. 여기까지는 바뀐 게 없다. 세 겹을 다 통과하면 (101, 15) 형태의 특징 맵이 나오고, Flatten이 이걸 1,515개짜리 1차원 벡터로 펼친다.


```python
prev = layers.Concatenate()([layers.Flatten()(prev), accessibility])
```

이전 모델에서는 이 1,515개 벡터가 곧바로 Dense(1)로 들어가서 최종 예측을 만들었다. 하지만 이번에는 Concatenate라는 연산이 들어간다. Concatenate는 이름 그대로 "이어붙이기"인데 1,515개짜리 벡터의 끝에 접근성 숫자 1개를 붙인다. 그러면 1,516개짜리 벡터가 된다. 앞쪽 1,515개는 "이 서열에 어떤 패턴들이 있는가"라는 정보이고, 맨 마지막 1개는 "이 구간의 크로마틴이 얼마나 열려 있는가"라는 정보다.


```plain text
Conv1D 출력 Flatten → (1515,)   ┐
                                                 ├── Concatenate → (1516,)
크로마틴 접근성 스칼라 → (1,)     ┘
```

이 1,516개짜리 벡터가 Dense(1) 레이어로 들어간다. Dense 레이어는 1,516개 숫자 각각에 가중치를 곱하고 다 더해서 logit 하나를 만든다. 이때 접근성에 해당하는 가중치도 학습된다.

여기서 Concatenate의 위치가 핵심이다. 접근성 정보를 맨 처음에 서열과 함께 넣어버리는 게 아니라, Conv1D가 서열 분석을 다 끝낸 뒤에 합류시킨다. 왜 이렇게 설계했을까? Conv1D의 역할은 DNA 서열에서 모티프 패턴을 찾는 것이다. 크로마틴 접근성은 서열 패턴과는 전혀 다른 종류의 정보다. 이 둘을 처음부터 섞어버리면 Conv1D 필터가 혼란스러워질 수 있다. "나는 서열 패턴만 찾으면 되는 건데, 이 이상한 숫자는 뭐지?" 하게 되는 거다.
대신 Conv1D한테는 순수하게 서열 분석만 시키고, 그 분석 결과를 다 내놓은 뒤에 접근성 정보와 합친다. 그러면 최종 Dense 레이어가 두 정보를 종합해서 판단할 수 있다. "서열을 보니 JUND가 좋아할 만한 모티프가 확실히 있다. 그런데 접근성이 거의 0이네. 그러면 결합 못 하겠다." 이런 논리를 학습할 수 있는 구조가 된다.


###

#2 커스텀 배치 생성기

이전 모델에서는 DeepChem의 model.fit(train)을 호출하면 알아서 데이터를 배치로 나눠서 모델에 넣어줬다. 그런데 그 표준 파이프라인은 입력이 하나라고 가정하는데 우리 모델은 이제 입력이 두 개(DNA 서열과 접근성)여서 표준 도구로는 접근성 데이터를 끼워넣을 방법이 없다. 그래서 우리가 직접 배치를 만들어주는 generate_batches 함수를 생성한다. 

```python
span_accessibility = {}
for line in open('accessibility.txt'):
    fields = line.split()
    span_accessibility[fields[0]] = float(fields[1])
```
```plain text
{
  "chr22:20208963-20209064": 0.003902,
  "chr22:29673572-29673673": 0.004378,
  ...
}
```

먼저 접근성 데이터를 파이썬 딕셔너리로 올린다. accessibility.txt 파일을 한 줄씩 읽어서 "chr22:20208963-20209064" 같은 ID를 키(key)로, 0.003902 같은 접근성 값을 값(value)으로 저장한다. 이제 ID만 알면 접근성을 바로 찾을 수 있는 전화번호부가 만들어진 셈이다.


```python
def generate_batches(dataset, epochs):
    for epoch in range(epochs):
        for X, y, w, ids in dataset.iterbatches(batch_size=1000, pad_batches=True):
            yield ([X, np.array([span_accessibility[id] for id in ids])], [y], [w])
```


generate_batches 함수는 파이썬의 제너레이터다. 제너레이터는 일반 함수와 달리 return 대신 yield를 쓴다. 함수를 호출하면 값을 하나 내놓고 멈추고, 다음에 다시 호출하면 멈춘 데서 이어서 다음 값을 내놓는다. 자판기에 동전을 넣을 때마다 음료 하나씩 나오는 것과 비슷하다.


```python
for X, y, w, ids in dataset.iterbatches(batch_size=1000):
    # ids = ["chr22:20208963-20209064", "chr22:29673572-29673673", ...]
    accessibility_batch = np.array([span_accessibility[id] for id in ids])
    # accessibility_batch = [0.003902, 0.004378, ...]  shape: (1000,)
    yield ([X, accessibility_batch], [y], [w])
    #       ↑ 두 입력    ↑ 정답  ↑ 가중치
```

이 제너레이터가 하는 일을 단계별로 따라가보자. dataset.iterbatches(batch_size=1000)가 훈련 데이터에서 1,000개씩 뽑아서 X(서열 행렬들), y(정답들), w(가중치들), ids(ID 문자열들)를 내놓는다. 이때 ids가 핵심이다. ids에는 "chr22:20208963-20209064" 같은 문자열이 1,000개 들어 있다. 이 ID들로 아까 만든 딕셔너리를 조회하면 해당 배치의 접근성 값 1,000개를 얻을 수 있다.

그러면 yield로 내보내는 건 이런 구조다. 첫 번째 원소가 [X, accessibility_array]로, 두 개의 입력을 리스트로 묶은 것이다. X는 (1000, 101, 4) 형태의 서열 배치이고, accessibility_array는 (1000, 1) 형태의 접근성 배치다. 두 번째 원소가 [y]로 정답 레이블이고, 세 번째가 [w]로 샘플 가중치다. Keras 모델은 이 구조를 받아서 첫 번째 입력(features)에 X를, 두 번째 입력(accessibility)에 접근성 배열을 자동으로 연결한다.


```python
for i in range(20):
    model.fit_generator(generate_batches(train, epochs=10))
    print(model.evaluate_generator(generate_batches(train, 1), [metric]))
    print(model.evaluate_generator(generate_batches(valid, 1), [metric]))
```

배치를 제너레이터로 만들었으니, 학습과 평가도 제너레이터 전용 함수를 써야 한다. 이전에 model.fit()을 쓰던 자리에 model.fit_generator()를, model.evaluate() 자리에 model.evaluate_generator()를 쓴다.

학습 루프의 구조는 이전과 같다. 바깥 루프 20번, 안쪽 10 에포크씩 총 200 에포크를 돈다. 매 10 에포크마다 generate_batches(train, 1)과 generate_batches(valid, 1)로 각각 한 에포크 분량의 배치를 만들어서 훈련 세트와 검증 세트의 ROC-AUC를 출력한다.

generate_batches에 epochs 매개변수가 있는 이유는, fit_generator에 10 에포크를 시키려면 제너레이터가 10 에포크 분량의 배치를 내놓아야 한다. 그래서 generate_batches 내부에서 for epoch in range(epochs) 루프를 돌며 같은 데이터를 여러 번 반복해서 내보낸다. evaluate_generator에는 1 에포크만 필요하니 epochs=1을 넘긴다.

###

#3 정리

이전 모델은 DNA 서열이라는 단일 증거만으로 판단했다. 이번 모델은 서열 분석 결과에 크로마틴 접근성이라는 물리적 맥락 정보를 한 조각 더해준다. Conv1D 세 겹이 서열에서 1,515개의 패턴 특징을 뽑아내면, 거기에 접근성 숫자 1개를 이어붙여 1,516개짜리 벡터를 만들고, Dense 레이어가 이 모든 정보를 종합해서 최종 결합 확률을 내놓는다.

추가된 정보는 숫자 하나에 불과하지만, 그 하나가 "문이 열려 있는가?"라는 결정적인 질문에 대한 답이기 때문에 예측 성능에 의미 있는 차이를 만들어낸다. 아무리 완벽한 결합 모티프가 있어도 크로마틴이 닫혀 있으면 소용없다는 생물학적 현실을, 모델 구조에 직접 반영한 것이다.