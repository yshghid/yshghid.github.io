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

### 2. Decision Tree 슈도코드

```python
class DecisionTree:
    def __init__(self, max_depth=None, max_features=None):
        self.max_depth = max_depth
        self.max_features = max_features
        self.root = None

    def fit(self, X, y):
        self.root = self._build_tree(X, y, depth=0)

    def _build_tree(self, X, y, depth):
        # 종료 조건: 최대 깊이 도달 또는 y가 모두 동일
        if depth == self.max_depth or len(set(y)) == 1:
            return LeafNode(predicted_class=most_common_label(y))

        # 사용할 feature 무작위 선택
        features = random_subset(X.shape[1], self.max_features)

        # 가장 좋은 분할 찾기
        best_feat, best_thresh = find_best_split(X, y, features)

        # 분할 실행
        left_indices = X[:, best_feat] < best_thresh
        right_indices = ~left_indices

        # 자식 노드 재귀적으로 생성
        left = self._build_tree(X[left_indices], y[left_indices], depth + 1)
        right = self._build_tree(X[right_indices], y[right_indices], depth + 1)

        return DecisionNode(feature=best_feat, threshold=best_thresh, left=left, right=right)

    def predict(self, X):
        return [self._predict_one(x, self.root) for x in X]

    def _predict_one(self, x, node):
        # 리프 노드이면 예측값 반환
        if isinstance(node, LeafNode):
            return node.predicted_class

        # 분기 조건에 따라 왼쪽 또는 오른쪽으로 이동
        if x[node.feature] < node.threshold:
            return self._predict_one(x, node.left)
        else:
            return self._predict_one(x, node.right)


# 결정 노드
class DecisionNode:
    def __init__(self, feature, threshold, left, right):
        self.feature = feature
        self.threshold = threshold
        self.left = left
        self.right = right

# 리프 노드
class LeafNode:
    def __init__(self, predicted_class):
        self.predicted_class = predicted_class
```
