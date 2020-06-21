---
layout: post
title: 정보이론(Information Theory)
tags: [Information Theory]
image:
  path: /images/abstract-1.jpg
  feature: abstract-1.jpg
---

이번 글에서는 《Elements of Information Theory 2ed》의 4, 7, 8, 9, 10장의 내용을 정리해보려합니다.

## 4장

* **Entropy:** $E(x)=\sum_x p(x) \log \frac{1}{p(x)} \leq \log \vert \mathcal{X} \vert$
    * 등호는 $p(x)=\frac{1}{ \vert \mathcal{X} \vert}$ 일때 성립.
    * proof) Given uniform distribution $u$, $D(p\vert \vert u)=\sum p(x) \log{\frac{p(x)}{1/n}}=\log n - H(X) = \log \vert \mathcal{X} \vert - H(X) \geq 0$


* **KL Divergence:** $D(p\vert \vert q)=\sum_x p(x) \log \frac{p(x)}{q(x)}=E_p \log \frac{p}{q}$
    * KL Divergence는 항상 0보다 크거나 같다.
    * proof) $E_p \log \frac{p}{q} = -E_p \log \frac{q}{p} \geq \log(-E_p \frac{p}{q}) \geq \log(1) = 0$ by Jensen.
    
    
* **Mutual Information:** $I(X;Y)=\sum p(x,y) \log \frac{p(x,y)}{p(x)p(y)} $
    * $ = D({p(x,y)}\vert \vert {p(x)p(y)}) \geq 0$ : $x, y$가 독립일 시 0이다. 즉, 높은 $I$는 서로 높은 정보를 공유한다는 의미이다.
    * $=H(X)-H(X\vert Y)$
    * $=H(Y)-H(Y\vert X)$
    
  
* **Entropy Conditioning:** $H(Y\vert X)=\sum_k p(x=x_k)H(Y\vert x=x_k)$ 
    * $H(X) \geq H(X\vert Y) :$ Conditioning은 항상 Entropy를 감소시킨다.
    * proof) $I(X;Y) = H(X)-H(X\vert Y) \geq 0$
    
    
* **Typical Set:** $A^{(n)}_\epsilon$
    * $(x_1, ..., x_n) \in A^{(n)}_\epsilon$ $if$ $\vert -\frac{1}{n}\log p(x_1, ..., x_n) -H(X) \vert < \epsilon$
    * $\leftrightarrow 2^{-n(H(X)+\epsilon)} \leq p(x_1, ..., x_n) \leq 2^{-n(H(X)-\epsilon)}$
    * $(1-\epsilon)2^{n(H(X)-\epsilon)} \leq \vert A^{(n)}_\epsilon \vert \leq 2^{n(H(X)+\epsilon)}$
    * proof)
        * $Right$: $1 \geq \sum_{x\in A^{(n)}_\epsilon} p \geq \sum_{x\in A^{(n)}_\epsilon} 2^{-n(H(X)+\epsilon)} =  \vert A^{(n)}_\epsilon \vert 2^{-n(H(X)+\epsilon)}$
        * $Left$: $1-\epsilon < Pr\{A^{(n)}_\epsilon\} \leq 2^{-n(H(X)-\epsilon)}$
        
    * $n \rightarrow \infty$이면 $Pr\{A^{(n)}_\epsilon\}=1$이다. ($\because$ law of large numbers)

* **Random process:** A Sequence of random variables.
    * **Entropy rate:** $\lim_{n \rightarrow \infty} \frac{1}{n} H(X_1, ..., X_n)=H(\mathcal{X})$
        * 만약 수렴성이 보장된 경우 $H'(\chi)=\lim_{n \rightarrow \infty} \frac{1}{n} H(X_n\vert X_{n-1})$과 같음.
    * **Markov Chain:** $P(X_n \vert  X_{n-1}, ..., X_1) = P(X_n\vert X_{n-1})$
        * Stationary : $P(X_n\vert X_{n-1})=P(X_2\vert X_1)$
        * Stationary할 경우, limit probability $\mu$에 대해 Entropy rate가 $H'(\chi)=H(X_n\vert X_{n-1})=H(X_2\vert X_1)=\sum_i \mu_i H(X_i)$가 된다.
    * **Hidden Markov Model:** $Y_i = \phi(X_i)$ $where$ $\phi : Deterministic $ $Function$ $and$ $X_i : M.C.(Markov Chain)$ 

## 7장

* **Channel Capacity:** $C$
    * $C = max_{p(x)} I(X;Y)=max_{p(x)} H(Y) - H(Y\vert X)$
    * 즉, 특성 채널을 통과했을 때 가장 많이 얻을 수 있는 정보량을 의미한다.
    
* **Channel Coding Theorem:**
    * Channel Capacity $C$가 해당 Channel의 믿을 수 있는 최대 전송량이다.
    
* **DMC(Discreate memoryless channel):**
    * $(x^n, p(y^n\vert x^n), y^n)$ $where$ $p(y_k\vert x^k,y^{k-1})=p(y_k\vert x_k), x^k = (x_1, ..., x_k)$
    * 즉, 출력은 대응되는 입력에만 의존하고, 그 이전 입력과 출력에는 의존하지 않는 경우이다.

* **(M, n) Code for the channel ($\mathcal{X}, p(y\vert x), \mathcal{Y}$):**
    * (M, n) Code란 총 표현해야할 개수가 M이고, Code의 차원(Symbol의 개수)이 n일 때를 의미하며, 자세히는 아래와 같다.
    * Index: $\{1, ..., M\}$
    * Codebook: $\{x(1), ..., x(M)\}, x \in \mathcal{X}^n$
    * Channel: $x \rightarrow y, y \in \mathcal{Y}^n$
    * Decoder $g$: $y \rightarrow \{1, ..., M\}$
    
    
* **The rate of (M,n) Code:** $R$
    * $R=\frac{\log M}{N}$
    * 한 Symbol 당 전송가능한 Maximum bit를 의미한다.
    * 즉, 채널이 noiseless하면 $M=2^n \Rightarrow R=1$이 된다.
    
    
* **Achievable:**
    * A rate R은 다음과 같을 때 Achievable하다고 한다.
    * $\exists (\lceil 2^{nR} \rceil, n)$ s.t. $\lambda^{(n)} \rightarrow 0$ as $n \rightarrow \infty$
    * $where$ $\lambda^{(n)} = \max_{i\in\{1, ..., M\}} \lambda_i=\max_i \sum_{y^n} p(y^n \vert  X^n(i))\mathbb{1}(g(y^n)\neq i)$
    * $\lambda_i$은 해당 index의 error 확률의 합을 의미한다.

## 8장

* **Differential Entropy:** $h$
    * Probabilty density function $f$가 주어졌을 때, $f(x)=F'(x)$ $where$ $F(x) = Pr(X\geq x)$, 
    * $h(x)=-\int f(x) \log f(x) dx$가 Continous 변수에 대한 Entropy이다.
    
    * **Example 1:** Uniform 
        * $f(x) = \frac{1}{b-a}$ $for$ $x \in [a,b]$
        * $h(x) = -\int^b_a \frac{1}{b-a} \log \frac{1}{b-a} dx =\log(b-a)$
        * **※주의: Discirete와 다른 특성 - 음수가 가능하다※**
    
    * **Example 2:** Normal Distribution 
        * $f(x) = \frac{1}{\sqrt{2\pi \sigma^2}}e^{-\frac{x^2}{2\sigma^2}}$
        * $h(x) = -\int^\infty_{-\infty}\frac{1}{\sqrt{2\pi \sigma^2}}e^{-\frac{x^2}{2\sigma^2}} \left(-\frac{1}{2}\log(2\pi\sigma^2) - \frac{x^2}{2\sigma^2}\right) dx$
        * $= \frac{1}{2}\log(2\pi\sigma^2) + \frac{1}{2\sigma^2}\int^\infty_{-\infty}{x^2}\frac{1}{\sqrt{2\pi \sigma^2}}e^{-\frac{x^2}{2\sigma^2}}dx$
        * $= \frac{1}{2}\log(2\pi\sigma^2) + \frac{1}{2\sigma^2}E[X^2]$
        * $= \frac{1}{2}\log(2\pi e \sigma^2)$
        * $\therefore h(N(0,\sigma^2))=\frac{1}{2}\log(2\pi e \sigma^2)$
        
    
* **Typical Set:** $A^{(n)}_\epsilon$ (Discrete와 거의 유사)
    * $(x_1, ..., x_n) \in A^{(n)}_\epsilon$ $if$ $\vert -\frac{1}{n}\log f(x_1, ..., x_n) -H(X) \vert < \epsilon$
    * $(1-\epsilon)2^{n(h(X)-\epsilon)} \leq vol(A^{(n)}_\epsilon) \leq 2^{n(h(X)+\epsilon)}$
    

        
* **Continous vs. Dicreate Entropy:**
    * Continous한 분포를 Quantization하면 Dicreate와의 관계를 파악할 수 있다.
    * $f(x_i)\bigtriangleup = \int^{(i+1)\bigtriangleup}_{i\bigtriangleup}f(x)dx=p_i$
    * $H(X^\bigtriangleup)=-\sum p_i \log p_i = -\sum f(x_i) \bigtriangleup \log (f(x_i)\bigtriangleup)$
    * $ = -\sum f(x_i) \bigtriangleup \log f(x_i)-\sum f(x_i) \bigtriangleup \log \bigtriangleup$
    * $ = h(X) - \log \bigtriangleup$
    * $\therefore H(X^\bigtriangleup) = h(X) - \log \bigtriangleup$ $if$ $f$가 리만적분 가능할 때.
    

* **KL Divergence:** $D(f\vert \vert g)=\int f \log \frac{f}{g}$
    * 마찬가지로, KL Divergence는 항상 0보다 크거나 같다.
    
    
* **Mutual Information:** $I(X;Y)=\int f(x,y) \log \frac{f(x,y)}{f(x)f(y)} $
    * $ = D({f(x,y)}\vert \vert {f(x)f(y)}) \geq 0$
    * $=h(X)-h(X\vert Y)$
    * $=h(Y)-h(Y\vert X)$
    
    
* **기타 특성:** 
    * $h(X+c)=h(X)$
    * $h(aX)=h(X)+\log\vert a\vert \neq h(X)$ **※주의: Discirete와 다른 특성 - 상수배시 Entropy가 변한다※**
    
    
* **Maximum Entropy:**
    * Discrete와 다르게 정의역이 $(-\infty, \infty)$가 되어, Uniform한 distribution이 소용없다.
    * 따라서 $X$의 평균이 0, 분산이 $E[XX']$일 때에 한하여 Maximum Entropy를 구해볼 수 있는데, 이 때 놀랍게도 평균과 분산이 같은 Guassian 분포 $\phi$가 Entropy가 최대가 된다.
    * proof) 
        * $D(g\vert \vert \phi) = \int g \log \frac{g}{\phi} = -h(g) - \int g(x) \log \phi(x) dx$
            * 한편, $\log \phi$는 $x$에 대한 2차 함수가 되며, $g$는 평균과 분산이 $phi$와 같기 때문에 $\int g(x) \log \phi(x) dx=\int \phi(x) \log \phi(x) dx$이다.
        * $D(g\vert \vert \phi) = -h(g) - \int \phi(x) \log \phi(x) dx = -h(g)+h(\phi) \geq 0$
        * $\therefore h(\phi) \geq h(g) $

## 9장

* **Gaussian Channel:**
    * $Y_i = X_i + Z_i$, $Z_i ~ \mathcal{N}(0, N)$인 Channel을 말한다. $X_i$와 $Z_i$는 독립이다.
    * 단, 이 때 만약 $X_i$의 크기 제한이 없으면 무조건 그 크기를 크게하여 $Z_i$인 noise를 무시하게 할 수 있다.
    * 따라서, 일반적인 경우 아래와 같은 제한 조건을 추가한다.
    * $\frac{1}{n}\sum^n_{i=1} x_i^2 \leq P$
    * 이를 **Power Constraint**라고 한다.
    

* **Probability Error:** $P_e=Pr\{\hat{X}(Y)\neq X\}$
    * 주어진  입력 $X$에 대한 출력 $Y$가 주어졌을 때, 기존 입력을 유추한 $\hat{X}$가 원래 입력 $X$와 다를 확률의 의미한다.
    * **Example:** Binary
        * $Pr(X=\sqrt{P}) = \frac{1}{2}, Pr(X=\sqrt{-P}) = \frac{1}{2}$ under power constraint $\frac{1}{2}\sum^2_{i=1}x^2_i=P$
        * $P_e=\frac{1}{2}Pr(Y<0\vert X=\sqrt{P})+\frac{1}{2}Pr(Y>0\vert X=\sqrt{-P}) = Pr(Z>\sqrt{P})$    
        

* **Fano's Ineqaulity:** $H(P_e)+P_e \log \vert \mathcal{X}\vert  \leq H(X\vert Y)$


* **Capacity for Gaussian Channel:**
    * $C=\max_{f(X):E(X^2)\leq P} I(X;Y)$
    * $=\max h(Y) - h(Y\vert X)$ 
    * $=\max h(Y) - h(Z)$ $\because Y=X+Z$
    * $=\max h(Y) - \frac{1}{2}\log 2\pi e N)$ $\because Z ~ \mathcal{N}(0, N)$
    * $\leq \frac{1}{2}\log 2\pi e (P+N) - \frac{1}{2}\log 2\pi e N)$ $\because E[Y^2]=E[X^2]+E[Z^2]$
    * $= \frac{1}{2}\log 2\pi e (1 + \frac{P}{N})$
    * 즉, Capacity가 SNR(Signal-to-noise ratio) Signal Power$(P)$ $/$ Noise Power$(N)$로 표현된다.


* **Parallel Gaussian Channel:**
    * 위와 같은 Gaussian Channel이 여러 개가 있다면 어떻게 될까?
    * $Y_j = X_j + Z_j$
    * $C = \max_{\sum E[X] \geq P} I(X_1, ..., X_k;Y_1, ..., Y_k)$
    * $= \max_{\sum E[X] \geq P} h(Y_1, ..., Y_k) - h(Y_1, ..., Y_k\vert X_1, ...,X_k)$
    * $\leq \max_{\sum E[X] \geq P} \sum \{h(Y_i) - h(Z_i)\}$
    * $= \max_{\sum E[X] \geq P} \sum_i \frac{1}{2} \log (1+\frac{P_i}{N_i})$
    * 안타깝게도 여기서 끝이 아니라, $P_i$를 $\sum P_i \leq P$에 맞게 배분하여 최대화해야한다.
    * 이를 Power Allocation Problem이라고 한다.


* **Power Allocation Problem:**
    * 위 문제에서 $\max_{\sum P_i \leq P} \sum_i \frac{1}{2} \log(1+\frac{P_i}{N_i})$를 풀려면 라그랑지안을 활용하면 된다.
        * $\min f(x)$
        * $s.t$ $g_i(x) \leq 0, h_j(x)=0$
        * 이를 라그랑지안으로 변환하면 아래와 같다.
        * $\min L=f(x) + \sum_i \lambda_i g_i(x) + \sum_i v_i h_i(x)$
        * $s.t. g_i(x) \leq 0, h_j(x)=0, \lambda_i \geq 0, \lambda_i g_i(x) = 0$
        * 최적해는 $\nabla_x L=0$일 때 성립한다.   
    * 이를 활용하면 정답은 아래와 같다.
        * $L=-\sum \frac{1}{2} \log(1+\frac{P_i}{N_i}) + \lambda (\sum P_i - P)$이고,
        * $\nabla_{P_i} L=-\frac{1}{2}\frac{1}{N_i+P_i} + \lambda = 0 \rightarrow \frac{1}{2\lambda}-N_i = P_i$가 된다.
        * $\frac{1}{2\lambda}=v$라 하면, 정답은 $\sum (v-N_i)^+=P$인 $v$에 대해 $P_i=(v-N_i)^+=max(v-N_i, 0)$가 된다.
    * 즉, 최종 Capacity는 $C=\sum_i \frac{1}{2} \log\left(1+\frac{(v-N_i)^+}{N_i}\right)$이다.
    * 이 때 정답이 $v$라는 최대 흐름이 주어졌을 때 각 Noise Power $N_i$에 맞게 $P_i$를 배분하는 것이므로, 물을 채워 넣는 것과 같다하여 **Waterfilling**이라고 부른다.


* **Time Correlated Noise:**
    * 만약 $Z_i$가 Parallel한 경우가 아니고, Time Correlated 되어있는 경우라면 어떨까?
    * 우선, Power Constraint 역시 Time Correlated 되어 있으므로, 아래와 같이 전체 Time에 대한 평균으로 재정의한다.
        * $E[\frac{1}{n} \sum x^2_i]=\frac{1}{n}\sum E[X^2_i]=\frac{1}{n}tr({E[X^2_i]})=\frac{1}{n}tr(K_X)\leq P$
    * $C = \max I(X_1, ..., X_k;Y_1, ..., Y_k)$
    * $= \max h(Y_1, ..., Y_k) - h(Y_1, ..., Y_k\vert X_1, ...,X_k)$
    * $\leq \max \sum h(Y_1, ..., Y_k) - h(Z_1, ..., Z_k)$
        * $Z_i$가 Time correlated 되어있어 $h(Z_1, ..., Z_k)=\sum h(Z_i)$으로 표현이 불가능하다.
    * $\leq \max \frac{1}{2}\log (2\pi e)^n \vert K_X+K_Z\vert  - \frac{1}{2}\log (2\pi e)^n \vert K_Z\vert $
        * $K_X$: Input Covariance, $K_Z$: Noise Covariance
    * $\leq \max_{\frac{1}{n}tr(K_X)\leq P} \frac{1}{2}\log \frac{\vert K_X+K_Z\vert }{\vert K_Z\vert }$
    * 최종 솔루션에 도달하기 위해서는 우선 아래와 같이 문제를 변형한다.
        * $K_Z=Q\Lambda Q^T$ (by Eigen Decomposition)
        * $\vert K_X+K_Z\vert =\vert K_X+Q\Lambda Q^T\vert =\vert Q(Q^T K_X Q + \Lambda)Q^T\vert $
        * $= \vert QQ^T\vert \vert Q^T K_X Q + \Lambda\vert =\vert Q^T K_X Q + \Lambda\vert $
        * $= \vert A+\Lambda\vert $
        * $\leq \Pi_i (A_{ii}+\lambda_i)$ (by Harmard inequailty, 등호는 A가 Diagonal일 때 성립한다)
        * $tr(A) = tr(Q^T K_X Q) = tr(K_X) = \sum A_{ii}\leq nP$
    * 즉, 아래와 최종적으로 표현 가능하다.
        * $\max_{\sum A_{ii}\leq nP} \sum \frac{1}{2}\log \frac{A_{ii}+\lambda_i}{\lambda_i}$
        * 이는 **Waterfilling**과 동일한 식이 되어, 해는 아래와 같다.
        * $A_{ii}=(v-\lambda_i)^+$, $\sum(v-\lambda_i)^+=nP$
    * 총 **n개의 Channel**에 대한 최종 Capacity는 $C=\sum\frac{1}{2}\log \left(1+\frac{(v-\lambda_i)^+}{\lambda_i}\right)$
    * **각 Channel**에 대한 Capacity는 $C_n=\sum\frac{1}{2n}\log \left(1+\frac{(v-\lambda_i)^+}{\lambda_i}\right)$


* **Gaussian Channels with Feedback:**
    * 만약 Feedback이 가능하다면 어떨까? Time Correlated에서 $Z_i$를 보고 $X_{i+1}$를 수정할 수 있는 것이다.
    * 따라서, Feedback이 $X=BZ+V$로 표현된다고 해보자. (즉, $X$와 $Z$가 Correlated 되어있다)
    * 그럼 다음과 같이 Capacity를 표현할 수 있다.
        * $C_{n,FB}=\max_{\frac{1}{n}tr(K_X)\leq P} \frac{1}{2n}\log \frac{\vert (B+I)K_Z(B+I)^t+K_V\vert }{\vert K_Z\vert }$ 
    * 이 경우 완전히 해는 풀지 못하지만 다음과 같이 Bound를 얻어낼 수 있다.
        * $C_{n,FB}\leq C_n+\frac{1}{2}$ and $C_{n,FB}\leq 2C_n$ (증명은 생략한다)

## 10장

* **Analog Data:** Analog Data는 시간에 대해 연속적인 함수이므로 디지털로는 무한한 저장공간이 필요하다. 즉, 완벽한 압축과 복원은 절대 불가능하다.


* **Lloyd Algorithm:** 최적의 압축 구간 선정 후 평균을 구해서 다시 압축 구간을 선정하는 행위를 반복하는 것.
    * $R_i = \{ x$ $\vert$ $\vert \vert x-\hat{x}_i\vert \vert   < \vert \vert x-\hat{x}_j\vert \vert $ $for$ $j\neq i\}$ 
    * $\hat{x}_i = E[X\vert k_i] = \frac{\int_{R_i}xf(x)dx}{\int_{R_i}f(x)dx}$
    * 위 과정을 반복한다.
    

* **Distortion:** 복원된 정보와 기존 정보의 차이를 말한다.
    * $d(x^n, \hat{x}^n)=\frac{1}{n}\sum^n_{i=1}d(x_i, \hat{x}_i)$
    * $D = \mathbb{E}[d(x^n, \hat{x}^n)]$
    

* **Acheivable:** 
    * $(R, D)$는 다음과 같을 때 Achievable하다고 말한다. 
    * $\exists (2^{nR}, n)$ $with$ $\lim_{n\rightarrow \infty} \mathbb{E}[d(x^n, \hat{x}^n)] \leq  D$
    * 즉, $nR$ bits로 표현한 $X$에 대해 달성할 수 있는 Distortion 정도인 $D$의 집합 의미한다.
    
* **Rate distortion region:** Achievable한 $(R, D)$의 Closed Set을 말한다.


* **The rate distortion function:** $R(D)$
    * $R(D)=\inf_R \{(R, D)\in$ $Rate distortion region$ $\}$
    * 즉, 허용된 Distortion D가 주어졌을 때 최소 각 자리당 bit가 필요한지를 함수로 나타내는 것이다.
    * $R=0$이면 특정 정보를 모두가 알고 있으므로 압축할 필요가 없는 정보를 의미한다.


* **The information rate distortion function:** $R^{(I)}(D)$
    * $R^{(I)}=\min_{p(\hat{x}\vert x):\sum p(x)p(\hat{x}\vert x)d(x,\hat{x})\leq D} I(X;\hat{X})$
    * 즉, 허용된 Distortion D가 주어졌을 때 최소 각 자리당 bit가 필요한지를 함수로 나타내는 것이다.
    * **Example 1:** $X \sim Bernouli(p)$ with **Hamming distortion**
        * $I(X;\hat{X})=H(X)-H(X\vert \hat{X})$
        * $=H(X)-H(X\oplus\hat{X}\vert \hat{X})$ ($\oplus$는 XOR 연산을 뜻한다)
        * $\geq H(X)-H(X\oplus\hat{X})$
        * $\geq H(X)-H(Pr(X\neq\hat{X}))$
        * $\geq H(X)-H(\mathbb{E}[d(x^n, \hat{x}^n)])$ (이렇게 $\mathbb{E}[d(x^n, \hat{x}^n)$를 식에 이끌어내는 것이 핵심이다)
        * $\geq H(X)-H(D)$ ($p<\frac{1}{2}$에서 $H(p)$는 증가함수)
            * **※주의: 여기서 끝나지 않고, R(D)는 음수가 될 수 없으므로, 0이 되는 범위를 잘 구해야한다.**
            * 특성상, 처음 0이 되는 지점 이후의 D에 대해서 0이다.
            * 참고로, 여기서는 $0\leq D \leq min(p, 1-p)$ 일 때 $H(X)-H(D)$이며, $D > min(p, 1-p)$이면 0이다.
    * **Example 2:** $X \sim N(0, \sigma^2)$
        * $I(X;\hat{X})=H(X)-H(X\vert \hat{X})$ with **squared-error distortion**
        * $=H(N(0, \sigma^2))-H(X-\hat{X}\vert \hat{X})$
        * $\geq H(N(0, \sigma^2))-H(X-\hat{X})$
        * $\geq H(N(0, \sigma^2))-H(N(0, E[(X-\hat{X})^2]))$
        * $= \frac{1}{2}\log2\pi e \sigma^2 - \frac{1}{2}\log2\pi e E[(X-\hat{X})^2] $
        * $\geq \frac{1}{2}\log \frac{\sigma^2}{D}$
            * $\frac{\sigma^2}{D} \geq 1$ 일 때 $\frac{1}{2}\log \frac{\sigma^2}{D}$이며, $\frac{\sigma^2}{D} < 1$이면 0이다.
