# Personal Project   
---------------------------  
## Description  
### 1. 데이터 수집.  
> * package : selenium, requests, BeautifulSoup, etc.  
> 
> 1. 일별로 카테고리별 랭킹에 따라 뉴스를 크롤링.  
>> * 네이버, 다음.  
>> * 뉴스는 본문,  신문사, 랭킹순위, 카테고리, 날짜, 사이트, 댓글수, 크롤링한 댓글수  
>>
>> 다음.  
>> * 키워드를 제공해주는 경우가 많아 키워드도 같이 크롤링하고 없는 경우에는 NaN으로 처리함.
>> * keyword를 추출하는 알고리즘을 이용하여 추가로 키워드를 추출.  
>>  
>> | category | date | link | press | rank | title | mainText | keywords | number of comments |  real comment | site |  
>> |:------------|:------:|:-----:|:-------:|:------:|:----:|:-------------:|:-----------:|:-----------------------:|:---------------:|------:|
>> | category | date|link | press | rank | title | mainText | keyword | number of comments |  real comment | site |  
>> 
>> 네이버.    
>> * 키워드를 제공해주지 않아 다음에서 동일한 기사를 찾아 키워드를 찾아서 크롤링함. 없는 경우에는 NaN으로 처리함.  
>> * keyword를 추출하는 알고리즘을 이용하여 추가로 키워드를 추출. 
>> 
>> | category | date | link | press | rank | title | mainText | number of comments |  real comment | site | keywords |  
>> |:------------|:------:|:-----:|:-------:|:------:|:----:|:-------------:|:----------------------------:|:--------------------:|:----:|--------------:|
>> | category | date | link | press | rank | title | mainText | number of comments |  real comment | site | keywords |  
>
> 2. 해당 뉴스에 대한 전체 댓글 내용과 각 댓글의 공감/비공감 수.  
>>   
>> 댓글.  
>> 
>> | category | date | link | comment | 공감 | 비공감 | site |  
>> |:------------|:------:|:-----:|:------------:|:-----:|:---------:|-----:|  
>> | category | date | link | comment | 공감 | 비공감 | site |  
>>  
> 3.  2시간마다 해당 시간대의 newstopic에 대한 순위를 카테고리별로 수집함.  
> * 이모티콘으로만 구성된 댓글은 배제함.
>>  
>> 뉴스토픽.  
>> 
>> | date | time | site | category | rank | topic |  
>> |:-------|:------:|:-----:|:-----------:|:------:|--------:| 
>> | date | time | site | category | rank | topic |    
>>

### 2. 데이터베이스 구축 (2017 12월 28일 15시 20분 기준).  
> 크롤링을 통해 데이터를 저장할 데이터 베이스를 구축.  
> mongoDB.  
> * DB 구축.  
>> * news - Daum : 3972.  
>> * news - Naver : 6241.  
>> * comments : 6450850.  
>> * newsTopic : 11088.  

### 3. 키워드 추출
> package : konlpy, customized-konlpy, etc. 
> * 자연어 처리를 위하여 cKonlpy와 mecab에 사용자 사전을 추가
> * 공공 데이터포털의 뉴스빅데이터 분석 정보 파일([빅카인즈](https://www.kinds.or.kr/) )을 이용하여 명사 그리고 키워드를 추출하고 이후 전처리 작업을 통해 명사만을 추가함.  
> * NIA 빅데이터 센터에서 2017년 2월에 제공한 한글형태소 사전을 cKonlpy와 mecab에 추가함.
> 국립국어원 우리말샘 사전과 및 SNS 분석기업 인사이터에서 자체 보유한 사전을 기반으로 최신 단어로 구성된 형태소 사전(NIADic) 제작(총 93만 단어)
>> * 국립국어원 ‘우리말샘’ : 기존 ‘표준국어대사전’에 수록된 50만 단어와 신어·생활어 7만5000단어, 지역어 9만단어, 전문용어 35만단어를 더해 모두 100만여 단어로 구성
>> * 인사이터 자체사전 : 브랜드, 유명인, 장소, 신조어 등의 명사 위주로 모두 50만여 단어로 구성
>> 구글의 pagerank를 이용한 키워드를 추출하는 코드가 있어 이를 이용하여 키워드를 추출하였으나, 다음으로부터 제시된 키워드와 기사내용을 읽어보고 내가 생각한 키워드 사이에 큰 차이를 보임. 
>> * Mecab과 ckonlpy를 형태소 분석기를 사용하여 각각의 뉴스에 대하여 10개씩의 키워드를 추출. 
>>> * 추출된 20개의 키워드에 대하여 다시 한번 mecab과 ckonlpy를 이용하여 형태소 분석을 실시하여 조사나 부사 등의 형태로 판별되는 것은 제외하고 나머지에 대하여 키워드를 3~5개로 추리는 작업을 실시
>>> * 예시)  
>>> ckonlpy ['이영학', '변호사', '재판', '사건', '아버지', '있을', '친구', '다고', '이영학 이영학의', '이영학의']  
>>> -> ['변호사','재판','사건','아버지','있을','친구','다고','이영학 이영학의'].  
>>> -> ['변호사','재판','사건','아버지','친구','이영학 이영학의']  
>>> -> ['변호사','재판','사건','아버지','친구','이영학']  
>>> mecab ['이영학', '변호사', '다고', '사건', '친구', '국민', '생각', '필요', '부탁', '더라']  
>>> -> ['이영학','변호사','사건','친구','국민','생각','필요','부탁']  
>>> -> ['이영학','변호사','사건','친구'] , ['재판'], ['국민','생각','필요','부탁']  
>>> * 기준)  
>>> 2개의 형태소 분석기를 사용하여 나온 10개의 키워드에 대하여 2개에서 모두 나온 키워드는 우선으로 사용하는 것을 원칙으로 함.
>>> mecab과 ckonlpy로부터 명사로 판정된 것을 우선으로 처리함. 

### 4. 키워드 분석
> * 뉴스 본문에서 추출한 키워드에 대하여 요일별 키워드 빈도를 분석하여 워드클라우드를 그릴수 있으며, 또한 추출된 키워드에 대하여 긍부정 분석을 진행하여 해당 뉴스가 키워드에 대하여 긍정적으로 서술하였는지 또는 부정적으로 서술하였는지를 파악할 수 있다. 
> * 요일별 랭킹뉴스에서 키워드의 빈도를 추출하여 요일별 키워드를 파악할 수 있다. 
> * 동일한 키워드에 대하여 여러 신문사의 긍부정 분석을 파악하여 논조를 파악할 수 있다. 
> * 동일한 키워드를 가진 뉴스에 대하여 댓글의 성향이 얼마나 다른지에 대하여 분석을 해보고자 함. 
> * 신문사, 키워드 등의 정보를 이용하여 분류
> * 신문사, 키워드 등의 정보를 이용하여 클러스터링
> * 시각화 작업
> * 키워드 사이의 연관성

### 5. 댓글 분석
> * 뉴스 본문에서 추출된 키워드에 대하여 해당 뉴스의 댓글들에 대하여 감정분석을 실시하여 긍정적인 반응인지 부정적인 반응인지를 파악할 수 있다. 

### 6. 감정분석을 위한 데이터 수집
> naver movie sentiment, 공공 데이터포털의 뉴스빅데이터 분석 정보 파일([빅카인즈](https://www.kinds.or.kr/) )중 주제에 따라 감정분석과, 긍정문장, 부정문장을 가지고 있는 문서가 존재하여 이를 이용하고자 함. 
>  * 공공 데이터포털의 뉴스빅데이터 분석 정보 파일
>> 긍정 문장 : 156291  
>> 긍정 단어 : 632330  
>> 부정 문장 : 161297  
>> 부정 단어 :  1617233  
> * naver movie sentiment analysis   
>> 20만건  

### 7. 웹 구축. 
> Flask를 이용하여 Amazone Web Service(AWS)에 웹 구축 (예정). 

---------------------------  
### * 참고
* [konlpy](konlpy.org)
* [customized konlpy](https://github.com/lovit/customized_konlpy)
* [datascienceschool](https://datascienceschool.net/)
* [공공데이터포탈](https://www.data.go.kr/)
> [데이터셋](https://www.data.go.kr/dataset/15012945/fileData.do)
* [빅카인즈](https://www.kinds.or.kr/)
---------------------------  