---
layout: post
title: 문자열로 Python 코드 실행하기
tags: [Python]
image:
  path: /images/abstract-1.jpg
  feature: abstract-1.jpg
---

이번 글에서는 문자열로 Python을 실행하는 방법에 대해 알아보겠습니다.

참고 자료 :
> [https://saelly.tistory.com/171](https://saelly.tistory.com/171)

> [https://kongdols-room.tistory.com/39](https://kongdols-room.tistory.com/39)

## 1. eval

eval은 문자열로 구성된 **식**을 실행하는 함수입니다.

`In[]`
```python
{% raw %}1+3{% endraw %}
```
`Out[]`



    4



`In[]`
```python
{% raw %}eval('1+3'){% endraw %}
```
`Out[]`



    4



`In[]`
```python
{% raw %}a = 3
a = eval('a + 3'){% endraw %}
```

이를 활용하면, 변수의 이름을 통해 값에 접근할 수 있습니다.

`In[]`
```python
{% raw %}a = 1
b = 2
c = 3

for var in ['a', 'b', 'c'] :
    print(eval(var)){% endraw %}
```
`Out[]`
    1
    2
    3
    

한편, eval은 문자열로 구성된 **식**이 아닌 **문**은 처리할 수 없습니다.

`In[]`
```python
{% raw %}eval('a=3'){% endraw %}
```
`Out[]`

    Traceback (most recent call last):
    

      File "C:\Users\slcf\Anaconda3\lib\site-packages\IPython\core\interactiveshell.py", line 2963, in run_code
        exec(code_obj, self.user_global_ns, self.user_ns)
    

      File "<ipython-input-5-2170d83d94ef>", line 1, in <module>
        eval('a=3')
    

      File "<string>", line 1
        a=3
         ^
    SyntaxError: invalid syntax
    


## 2. exec

따라서, **문**을 처리할 때는 exec 함수를 사용하게 됩니다.

`In[]`
```python
{% raw %}exec('a=10')
print("a:", a)

exec('b=20')
print("b:", b){% endraw %}
```
`Out[]`
    a: 10
    b: 20
    

물론 eval과 마찬가지로 string 인자를 전달할 수도 있습니다.

`In[]`
```python
{% raw %}a = 1
b = 2
c = 3

for var in ['a', 'b', 'c'] :
    s = var + ' += 1'
    exec(s)

print("a:", a, "b:", b, "c:", c){% endraw %}
```
`Out[]`
    a: 2 b: 3 c: 4
    

반면, exec 함수는 **문**을 처리하기 때문에 **식**은 아무런 의미를 가지지 못합니다.

`In[]`
```python
{% raw %}exec('a+1'){% endraw %}
```

## 3. 활용 방법

위의 함수들을 활용하면, PyTorch에서의 모델을 구성할때도 변하게 할 수 있습니다.

`In[]`
```python
{% raw %}import torch
import torch.nn as nn

class CNN(nn.Module):
    def __init__(self) :
        super(CNN, self).__init__()
        
        # 똑같은 구조지만 이름만 다른 5개의 Layer를 만들고 싶을 때
        # 아래와 같이 format을 활용하면 반복없이 만들 수 있습니다.
        s = '''self.layer{} = nn.Sequential(
            nn.Linear(100, 10),
            nn.ReLU(),
            nn.Linear(10, 1)
        )'''
        
        for i in range(5) :
            exec(s.format(i))
            
    def forward(self, input) :
        
        output = []
        
        # 결과 값 역시 다른 5개의 Layer에서의 출력 값을 가져오기 위해
        # List에 append하는 식을 exec을 통해 실행시킬 수 있습니다.
        s = '''output.append(self.layer{}(input))'''
        
        for i in range(5) :
            exec(s.format(i))
            
        return torch.cat(output, dim=1)
    
model = CNN(){% endraw %}
```

`In[]`
```python
{% raw %}# exec을 활용하였음에도 불구하고 잘 구현된 것을 확인할 수 있습니다.
model{% endraw %}
```
`Out[]`



    CNN(
      (layer0): Sequential(
        (0): Linear(in_features=100, out_features=10, bias=True)
        (1): ReLU()
        (2): Linear(in_features=10, out_features=1, bias=True)
      )
      (layer1): Sequential(
        (0): Linear(in_features=100, out_features=10, bias=True)
        (1): ReLU()
        (2): Linear(in_features=10, out_features=1, bias=True)
      )
      (layer2): Sequential(
        (0): Linear(in_features=100, out_features=10, bias=True)
        (1): ReLU()
        (2): Linear(in_features=10, out_features=1, bias=True)
      )
      (layer3): Sequential(
        (0): Linear(in_features=100, out_features=10, bias=True)
        (1): ReLU()
        (2): Linear(in_features=10, out_features=1, bias=True)
      )
      (layer4): Sequential(
        (0): Linear(in_features=100, out_features=10, bias=True)
        (1): ReLU()
        (2): Linear(in_features=10, out_features=1, bias=True)
      )
    )



`In[]`
```python
{% raw %}# 마찬가지로 결과가 제대로 나오는 것을 확인할 수 있습니다!
model(torch.randn(128, 100)).shape{% endraw %}
```
`Out[]`



    torch.Size([128, 5])


