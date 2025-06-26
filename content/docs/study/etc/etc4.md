---
date : 2025-06-27
tags: ['2025-06']
categories: ['AI']
bookHidden: true
title: "#4 Random Forest pseudocode로 이해하기"
bookComments: true
---

# #4 Random Forest pseudocode로 이해하기

#2025-06-27

---

### 1. Random Forest 분류 슈도코드

```python
class RandomForestClassifier:
    def __init__(self, n_trees, max_features, max_depth):
        self.n_trees = n_trees                  # 트리 개수
        self.max_features = max_features        # 각 노드에서 무작위로 선택할 feature 수
        self.max_depth = max_depth              # 트리 최대 깊이
        self.trees = []                         # 의사결정트리 저장 리스트

    def fit(self, X, y):
        for _ in range(self.n_trees):
            # 1. 부트스트랩 샘플링 (데이터 중복 허용 샘플링)
            X_sample, y_sample = bootstrap_sample(X, y)

            # 2. 의사결정트리 학습 (노드마다 무작위 feature 선택)
            tree = DecisionTree(max_features=self.max_features, max_depth=self.max_depth)
            tree.fit(X_sample, y_sample)
            
            self.trees.append(tree)

    def predict(self, X):
        # 각 트리로부터 예측 결과 수집
        tree_preds = [tree.predict(X) for tree in self.trees]

        # 각 샘플에 대해 다수결(Majority Vote)
        final_preds = []
        for i in range(len(X)):
            votes = [pred[i] for pred in tree_preds]
            final_preds.append(majority_vote(votes))

        return final_preds

# 보조 함수 (부트스트랩 샘플링)
def bootstrap_sample(X, y):
    n_samples = len(X)
    indices = np.random.choice(n_samples, size=n_samples, replace=True)
    return X[indices], y[indices]

# 보조 함수 (다수결)
def majority_vote(votes):
    return most_common_label(votes)
```
