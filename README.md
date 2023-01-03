# University_SWContest2021

_Social-media 기반의 텍스트 마이닝을 통해 주요 인사이트를 도출하여 지자체의 의사결정에 지원해보기 위해 프로젝트를 수행함._
⭐

## 데이터 수집
<ul>
  <li> BeautifulSoup, Selenium 이용</li>
  <li> Naver(블로그/카페/지식인), Daum(블로그/카페) 포스팅 제목 및 본문 크롤링</li>
  <li> Naver 약 3,600건, Daum 약 5,500건, 총 9,173건의 데이터 수집</li>
  <li> 크롤링 코드는 별도의 repository를 생성하여 올릴 예정</li>
</ul>

<br>

## 데이터 전처리
<ul>
  <li> 홍보, 광고, 스팸 목적성 문서 제거</li>
  <li> 한글 외 문자 제거</li>  
  <li> Konlpy Komoran 이용한 형태소 분석 및 명사 추출</li>  
  <li> 불용어(무의미하거나 불필요한 단어) 제거</li>
</ul>

[불용어 사전 참조](https://www.ranks.nl/stopwords/korean) 

<br>

## LDA 토픽 모델링
### LDA 프로세스
<ol>
<li><b> 토픽 개수 결정 (사용자가 지정)</b></li>
  - 전체 문서 데이터셋에서 추출할 토픽의 총 개수는 하이퍼파라미터로써 사용자가 직접 지정해야 한다.</br>
  - <b>토픽 수에 따른 성능 평가를 진행하여 최선의 토픽 개수를 선정한다.</b></br>
  - 일반적으로 토픽 모델링에서 토픽의 개수는 k로 표현하며 전체 문서의 개수는 M으로 표현한다.</br></br>
  
<li><b> 단어의 토픽 할당</b></li>
  - <b>LDA 알고리즘이 M개의 모든 문서 내 모든 단어를 k개의 토픽 중 하나로 임의로 할당하는 작업을 수행한다.</b></br> 
  - 이 작업을 마치면 모든 문서는 토픽을 갖게 되며 각 토픽은 단어 분포를 갖게 된다.</br>
  - 모든 단어를 각각 임의의 토픽에 할당하였기 때문에 토픽 내 단어 분포의 결과는 틀린 상태이다.</br></br>

<li><b> 확률분포 기반 토픽 재할당</b></li>
  - <b>모든 단어에 대해 토픽을 재할당하기 위해, 모든 할당이 완료될 때까지 3-1과 3-2를 반복 수행한다.</b></br>
  - LDA는 문서 내 어떤 단어는 잘못된 토픽에 할당된 상태이지만, 다른 모든 단어들은 모두 올바른 토픽에 할당되어 있다고 가정한다.</br> 
  - 아래 3-1과 3-2와 같은 기준에 따라 토픽을 재할당한다. 아래의 2개의 문서를 예로 들어보겠다.</br></br>
  <img src="https://user-images.githubusercontent.com/103558593/210326163-2bd1ac45-4a55-4b1d-9130-62d59095d27d.png", width="800", height="400"><br><br>
  
  <ol>
  <li><b> 같은 문서 내 단어의 토픽 분포 비교</b></li>
    - 문서 1에서 3번째 단어인 '배'의 토픽을 할당하려고 한다.<br>
    - <b>첫 번째로, 단어가 속한 문서의 다른 단어들이 어떤 토픽에 할당되었는지 확인한다.</b><br>
    - 문서 1 내 모든 단어들은 토픽 A와 토픽 B에 50:50의 비율로 할당되어 있기 때문에, '배'라는 단어는 토픽 A, B 어디에도 할당될 수 있다.<br>
    - 이러한 경우 단어를 올바르게 재할당하기 어렵기 때문에 3-2로 넘어간다.<br><br>
  
  <li><b> 타 문서 내 단어의 토픽 분포 비교</b></li>
    - <b>두 번째로, 재할당하려는 단어 '배'가 다른 문서까지 포함한 전체 문서에서 어떤 토픽에 할당되었는지 확인한다.</b><br>
    - 문서 1과 문서 2에 '배'라는 단어는 모두 토픽 B에 할당되어 있기 때문에, '배'라는 단어는 토픽 B에 재할당될 가능성이 높다.<br> 
    - 이처럼 3-1과 3-2의 두가지 기준을 참고하여 모든 문서의 모든 단어를 하나씩 재할당한다.<br><br> 
  </ol>
</ol>
-> LDA 토픽 모델링 결과, <b>문서별 토픽 분포</b>와 <b>토픽별 단어 분포</b>를 얻을 수 있다.<br><br>

> 문서별 토픽 분포

|**문서 구분**|토픽 분포|
|:---:|---|
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;문서 1&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|토픽 A 40% 토픽 B 60%&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
|문서 2|토픽 B 100%| <br>

> 토픽별 단어 분포

|**토픽 구분**|단어 분포|
|:---:|---|
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;토픽 A&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|배 0%, 사과 0%, 바나나 0%, 귤 0%, 맛 0%, <b>비행기 50%, 자동차 50%</b>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
|토픽 B|<b>배 50%, 사과 12.5%, 바나나 12.5%, 귤 12.5%, 맛 12.5%,</b> 비행기 0%, 자동차 0%|

<br>

### corpus 생성
LDA 모델에 들어갈 객체를 만든다.

```python
noun_dic = corpora.Dictionary(docs_n)
noun_dic.filter_extremes(no_below=3, no_above=0.9) # 빈도가 3미만 or 전체의 90% 이상인 단어 제외
corpus = [noun_dic.doc2bow(doc_n) for doc_n in docs_n] 
```
- Dictionary에 list of list of str 형식의 documents를 입력하면 Dictioanry가 학습된다.<br>
- 단어가 int 형식의 idx로 변환되어 단어와 단어별 고유 번호 사전이 생성된다.<br>
- 너무 독특한 단어나 흔한 단어는 사전에서 필터링 한다.<br>
- 사전 속의 단어가 문장에서 몇 번 출현하는지 빈도를 구해 벡터화한다.<br>
- Bag of Words(BoW)란 단어들의 문맥이나 순서를 무시하고, 각 단어에 빈도 값을 부여해 피쳐 값을 만드는 모델이다.<br>

```python
noun_dic[50]
'경기장'
```
- 50이라는 고유 번호를 가진 단어가 정수 인코딩이 되기 전에는 어떤 단어였는지 확인할 수 있다.
- 기존 단어는 ‘경기장’ 이였음을 알 수 있다.

<br>

### LDA 토픽 모델링
모델 성능 평가 지표 Perplexity, Coherence를 이용하여 최선의 토픽 수를 선정한다.
|**구분**|**Perplexity(혼잡도)**|**Coherence(일관성)**|
|:---:|---|---|
|**내용**|- 확률 모델이 결과를 얼마나 정확하게 예측하는지 판단<br>- 낮을수록 정확하게 예측 =모델이 문서를 잘 반영함|- 토픽이 얼마나 의미론적으로 일관성 있는지 판단&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<br>- 높을수록 의미론적 일관성 높음&nbsp;&nbsp;&nbsp;|
|**한계**|낮다고 해서, 결과가 해석 용이하다는 의미는 아님|너무 높아지면 정보의 양이 줄어들게 됨|

```python
Lda = gensim.models.ldamodel.LdaModel
perplexity_score=[]
coherence_score=[]

for i in range(1,10):
    ldamodel=Lda(corpus, num_topics=i, id2word=noun_dic, passes=15, iterations=200, random_state=0)
    perplexity_score.append(ldamodel.log_perplexity(corpus)) #혼잡도
    coherence_score.append(CoherenceModel(model=ldamodel, corpus=corpus, coherence='u_mass').get_coherence()) #일관성
```
- 토픽 수가 1~ 9개일 때 각각의 혼잡도와 일관성을 측정한다.
- LDA 파라미터
  - num_topics: 토픽 수 
  - passes: 전체 코퍼스 학습 횟수
  - interations: 문서 당 반복 횟수

```python
plt.plot(range(1,10),perplexity_score,'r',marker='^') #(x,y,color)
plt.xlabel("number of topics")
plt.ylabel("perplexity score")
plt.show()
```
<ul>
<img src="https://user-images.githubusercontent.com/103558593/210403305-3f61b7b0-ce4a-4809-8b7d-d0faa87be82a.png", width="400", height="200"><br><br>
</ul>

```python
plt.plot(range(1,10),coherence_score,'b',marker='o')
plt.xlabel("number of topics")
plt.ylabel("coherence score")
plt.show()
```
<ul>
<img src="https://user-images.githubusercontent.com/103558593/210403706-24179602-2abf-4774-acc5-e0fedd153e43.png", width="400", height="200"><br><br>
</ul>

```python
lda_model = Lda(corpus, num_topics=4, id2word=noun_dic, passes=15, iterations=200, random_state=0)
```
- 위에서 선정한 최선의 토픽 수로 토픽 모델링을 진행한다.

<br>

### 시각화
<ul>
  <img src="https://user-images.githubusercontent.com/103558593/210376012-55821337-02ad-4d83-84e8-250c97158c44.png", width="800", height="150"><br><br>
  <li>토픽별 단어 분포를 이용하여 각 토픽별 상위 랭크 단어를 확인한다.</li>
</ul>

- LDA Topic Modeling 해석 방법
  - 토픽 간 거리가 멀수록 판별 타당도가 높고 주제가 뚜렷하게 구분됨 
  - 토픽 원의 크기가 클수록 높은 빈도를 가진 단어들로 구성됨
  - 우측 파란막대그래프는 전체의 빈도를, 빨간막대그래프는 해당 토픽 내 빈도를 나타냄

<br>

## 토픽별 주요 키워드 확인
### 1. 워드클라우드 (빈도 기반 시각화)
<ul>
  <img src="https://user-images.githubusercontent.com/103558593/210391509-d2e901bf-f80e-41f9-836f-ad0d7605efef.png", width="800", height="300"><br><br>
  <li>가장 좌측의 그림: 전체 상위 빈도 단어 50개 시각화</li>
  <li>우측 1번 그림: Topic1 상위 빈도 단어 50개 시각화 -> &nbsp;<b>'이사/수리'</b>로 라벨링</li>
  <li>우측 2번 그림: Topic2 상위 빈도 단어 50개 시각화 -> &nbsp;<b>'웨딩'</b>으로 라벨링</li>
  <li>우측 3번 그림: Topic3 상위 빈도 단어 50개 시각화 -> &nbsp;<b>'입시/교육'</b>으로 라벨링</li>
  <li>우측 4번 그림: Topic4 상위 빈도 단어 50개 시각화 -> &nbsp;<b>'음식/맛집'</b>으로 라벨링</li>
</ul>

<br>

### 2. Word2Vec, TSNE (유사도 기반 시각화)
<li> Social-media 기반의 텍스트 마이닝을 통해 주요 인사이트를 도출한다.</li>

<br>

## 긍부정 분석
<li> Social-media 기반의 텍스트 마이닝을 통해 주요 인사이트를 도출한다.</li>

<br>

## 인사이트 도출
<li> Social-media 기반의 텍스트 마이닝을 통해 주요 인사이트를 도출한다.</li>

<br>

## 마무리하며..
<li> 이번 프로젝트에서는 빈도 기반 벡터화(BoW)를 진행하였지만, 다음엔 TfidfVectorizer를 이용하여 LDA를 수행해보고 차이를 알아봐야겠다.</li>
<li> Kmeans도 토픽모델링으로 이용 가능하다고 하는데, 다음엔 LDA가 아닌 k-means clustering을 이용하여 토픽모델링을 진행해봐야겠다.</li>

<br>

> LDA와 k-means의 차이

|**구분**|LDA|k-means|
|:---:|--|---|
|문서의 토픽 수에 대한 가정|한 문서에 여러 종류의 토픽이 존재할 수 있다 가정|k-means는 한 문서에 하나의 토픽만 존재한다고 가정|
|예시|영화 리뷰_ 한 문서에 여러 관점이 존재|뉴스_ 한 문서에 하나의 주제 존재|<br>

<!-- Place this tag where you want the button to render. -->
<a class="github-button" href="https://github.com/cherry317/University_SWContest2021/buttons/github-buttons" data-color-scheme="no-preference: light; light: light; dark: dark;" data-icon="octicon-star" data-size="large" data-show-count="true" aria-label="Star cherry317/University_SWContest2021 on GitHub">Star</a>
