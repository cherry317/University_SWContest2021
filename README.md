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



### 시각화

## 토픽별 주요 키워드 확인
<li> Social-media 기반의 텍스트 마이닝을 통해 주요 인사이트를 도출한다.</li>

### 1. 워드클라우드 (빈도 기반 시각화)
<li> Social-media 기반의 텍스트 마이닝을 통해 주요 인사이트를 도출한다.</li>

### 2. Word2Vec, TSNE (유사도 기반 시각화)
<li> Social-media 기반의 텍스트 마이닝을 통해 주요 인사이트를 도출한다.</li>

## 긍부정 분석
<li> Social-media 기반의 텍스트 마이닝을 통해 주요 인사이트를 도출한다.</li>

## 인사이트 도출
<li> Social-media 기반의 텍스트 마이닝을 통해 주요 인사이트를 도출한다.</li>

## 마무리하며..
<li> Social-media 기반의 텍스트 마이닝을 통해 주요 인사이트를 도출한다.</li>
