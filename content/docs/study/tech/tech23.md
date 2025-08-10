---
date : 2025-06-17
tags: ['2025-06']
categories: ['python']
bookHidden: true
title: "Related Study #2 Cluster detection algorithm"
bookComments: true
---

# Related Study #2 Cluster detection algorithm

#2025-06-17

---

돌연변이는 무작위로 발생하지만 실제 분포를 확인해보면 그렇지 않다.
- 엄연히 군집을 형성하고 있으며
- 이는 해당 돌연변이의 '생존'에 관여한 외부 요인의 존재를 보여준다.

###

논문 "Computational methods for detecting cancer hotspots"
- 암에서 반복적으로 관찰되는 돌연변이 즉 핫스팟(hotspot)을 식별하기 위한 계산적 방법 40여개에 대한 리뷰 논문.

- 암에서 Hotspot mutation은 여러 환자에서 동일한 위치에 반복적으로 나타나는 돌연변이로써 우연히 발생할 가능성이 낮기 때문에 기능적 역할을 할 가능성이 높다고 간주됨에 따라 무의미한 hotspot을 거르고 중요한 hotspot 식별을 위한 여러 알고리즘이 고안되었다. [1]

- 우리 데이터는 암 유전체가 아닌 바이러스 유전체이지만 돌연변이 빈도가 높은 위치를 hotspot mutation으로 보는 시각이 동일하며 우연히 발생할 가능성이 낮기 때문에 기능적 역할을 할 가능성이 높다 << 라는 가정 또한 일치하므로 중요한 hotspot 식별을 위해 고안된 해당 알고리즘들은 우리 알고리즘과 비교 대상으로 적절하다.

![image](https://github.com/user-attachments/assets/2c5349e9-bb2f-482b-8dc0-0feea2918c93)

#