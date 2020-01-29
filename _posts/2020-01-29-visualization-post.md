---
layout: post
title: 알아두면 유용한 Python 시각화 사이트 및 툴들
tags: [Python]
image:
  path: /images/abstract-1.jpg
  feature: abstract-1.jpg
---

이번 글에서는 알아두면 유용한 Python 시각화 사이트 및 툴들에 대해 알아보겠습니다.

## 1. Matplotlib

가장 기본적인 시각화 도구라고 할 수 있겠습니다. 다만, 가장 기본적인만큼 상호작용(interactive) 등에 한계가 있다는 것이 흠입니다. 

공식 사이트는 [https://matplotlib.org/](https://matplotlib.org/)이며, 각종 활용방법은 널리 알려져 있는 툴이니 구글에서 검색하는 것이 가장 빠른 방법입니다.

추가적으로 애니메이션(animation)도 지원하니 잘 활용하면 멋진 GIF를 만들 수 있을 듯합니다.


```python
{% raw %}
# Matplotlib import
import matplotlib.pyplot as plt

# Matplotlib animation
import matplotlib.animation as animation
{% endraw %}
```

Jupyter Notebook에서 사용하려면, 아래와 같이 matplotlib inline을 넣어주셔야 합니다. 다만 이 경우에는 애니메이션이 작동하지 않으니, 약간 아쉬운 점입니다.


```python
{% raw %}
import matplotlib.pyplot as plt
%matplotlib inline
{% endraw %}
```

혹시나, Matplotlib으로 만들 수 있는 그래프들을 한 눈에 확인하고 싶다면, [https://www.machinelearningplus.com/plots/top-50-matplotlib-visualizations-the-master-plots-python/](https://www.machinelearningplus.com/plots/top-50-matplotlib-visualizations-the-master-plots-python/)를 참고하는 것을 추천드립니다!

## 2. Seaborn

Seaborn은 약간 편해지고 색이 예쁘게 만들어주는 Matplotlib의 보조 도구라고 생각하시면 됩니다. 

주로 틀이 정해져 있는 그림들을 그릴 때 많이 활용됩니다. 예를 들면, Correalation Matrix와 Confusion Matrix 등이라고 말할 수 있겠습니다.

Tutorial은 다음 주소를 확인부탁드립니다 : [https://seaborn.pydata.org/tutorial.html](https://seaborn.pydata.org/tutorial.html)


```python
{% raw %}
import seaborn as sns
import matplotlib.pyplot as plt
%matplotlib inline
{% endraw %}
```

## 3. Plotly

Matplotlib이 가장 기본적인 도구들을 제공하고 있다면, Plotly는 3D 그래프 및 상호작용 기능 등 복잡한 기능을 구현할 수 있는 가장 보편적인  패키지입니다.

Plotly는 Jupyter Notebook 상에서의 공식 Tutorial도 제공하며, 해당 글은 [https://plot.ly/python/ipython-notebook-tutorial/](https://plot.ly/python/ipython-notebook-tutorial/)에서 확인할 수 있으며, 매우 잘되어 있기 때문에 사용전에 모두 한 번 보시는 것을 추천드립니다.


```python
{% raw %}
import chart_studio.plotly as py
{% endraw %}
```

## 4. Pyecharts

Plotly와 비슷한 기능들을 제공하지만, 조금 더 상호작용 가능한 차트들이 많은 Pyecharts입니다.

다만 기본 제공 언어가 중국어로 되어있어서, 영어 업데이트는 많이 느린 편입니다.

2019년까지만해도 잘 활용되었었는데, 2020년 1월 29일 현재는 패키지가 업그레이드되었음에도 불구하고, 영어 설명 문서는 업데이트되지 않아 기존 코드들이 돌아가지 않습니다.

공식 사이트는 아래에 있습니다.

[https://pyecharts.org/](https://pyecharts.org/)
