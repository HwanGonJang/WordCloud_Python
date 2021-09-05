# 파이썬 워드클라우드  with colab, jupyter

## 개요
제가 일하는 메이커스페이스에서 머그컵 전사를 위한 워드클라우드 만들기 행사가 진행되었습니다. 하얀색 머그컵에 워드클라우드를 전사하여 꾸미는 행사로 학생들과 여러 교직원 분들이 참여하셨습니다. 그리고 이 워드클라우드 제작을 위한 코드를 제가 짜게 되었고 이 프로젝트는 구글 코랩과 주피처 환경에서 작업하였습니다.

## 개발과정
워드클라우드를 만들기 위해 고려해야 할 것은 다음과 같습니다.

1. 워드클라우드의 데이터가 될 단어들 뽑아오기.
2. 영어만 사용 가능한 워드클라우드 라이브러리를 한글도 사용 가능하게 만들기.
3. 방대한 양의 데이터를 누구나 쉽게 가공 가능하게 하기.
4. 워드 클라우드 만들기

강의가 목적인 프로젝트이기 때문에 수강자들이 쉽게 따라올 수 있어야하고 데이터를 쉽고 빠르게 가공할 수 있어야 했습니다. 따라서 저는 데이터를 웹의 신문이나 글에서 가져오는 것으로 정하고 데이터 가공은 구글의 코랩을, 나머지 파싱과 워드클라우드 제작은 주피터 환경을 사용했습니다.

### 기술
* Python
  * 파이썬의 워드클라우드 라이브러리와 웹 파싱 기능을 활용할 수 있는 기본적인 워드클라우드 제작 언어
* Jupyter
  * 주피터 노트북은 코드를 독립적으로 짤 수 있어서 수강자들이 흐름을 이해하고 수정하기 적합합니다.
* Colab
  * 구글 코랩은 주피터와 비슷한 환경이나 자원을 로컬이 아닌 구글의 서버에서 사용하기 때문에 데이터 가공이나 인공지능 학습에서 유리합니다.


### 1. 웹에서 데이터 파싱하기
파이썬의 대표적인 기능에는 데이터 파싱이 있습니다. 파이썬의 라이브러리 beautifulsoup4 는 쉽게 웹의 html 데이터에서 원하는 부분을 가져올 수 있습니다.

우선, 아나콘다3를 다운받아주면 주피터 노트북이 자동으로 설치됩니다. 주피터 노트북을 실행하고 새로운 파이썬 파일을 만들어줍니다.

파싱을하기 위해선 먼저 웹사이트가 필요합니다. 워드클라우드로 만들고 싶은 주제를 선택하여 관련 웹을 찾아야합니다.

~~~python
import requests
from bs4 import BeautifulSoup

//웹 페이지 주소 가져오기
webpage = requests.get("https://ko.wikipedia.org/wiki/%EA%B5%AC%EA%B8%80")
soup = BeautifulSoup(webpage.content, "html.parser")
//글이 들어있는  div태그와 클래스 설정
result = soup.find('div', class_='mw-body-content mw-content-ltr')

//parsing 변수에 p태그 안의 내용을 for문으로 모두 저장
parsing = ""
for tag in result.find_all('p'):
    parsing += tag.text
        
print(parsing)

//text파일로 파싱한 데이터 저장
f = open('text.txt', 'w')
f.write(parsing)
f.close()
~~~

저는 위키피디아의 구글문서를 가져왔습니다. F12키를 누르면 크롬에서는 개발자 도구가 나타납니다. 이곳에서 웹의 글 부분만을 가져올 것입니다. 위키피디아의 경우 모든 문서가 글 부분이 div 태그의 mw-body-content 클래스 안의 p 태그로 작성되어있습니다.
p태그의 모든 내용을 긁어와서 parsing 변수에 저장해주고 다시 text 텍스트파일로 저장해줍니다. 이렇게 하면 파싱은 성공입니다.

<img src='https://github.com/HwanGonJang/HwanGonJang.github.io/blob/master/Pictures/w_1.png?raw=true'>


### 2. 파싱한 데이터 단어로 나누기
제가 사용할 파이썬의 워드클라우드 라이브러리는 기본적으로 영어만 지원합니다. 따라서 워드클라우드에 들어갈 단어를 가공하는 작업은 따로 해주어야합니다. 한글은 영어와 문장 체계가 달라 문장을 단어로 가공하기 위해서는 형태소로 분리하는 작업이 필요합니다. 다행히 형태소 분석기를 만들어 주신 분이 계셔서 저는 이 라이브러리를 가져와서 사용했습니다.

우선, 데이터 가공을 위해 구글 코랩을 열어줍니다. 새로운 프로젝트를 만들어서 데이터 가공 작업을 할 것입니다. 

~~~python
#utf-8, cp949, euc-kr
f = open('/content/text.txt', 'r', encoding='cp949')
txt = f.readlines()
f.close()
~~~

처음으로 이전에 파싱한 텍스트 파일을 가져와서 txt 변수에 저장해줍니다. 단, 코랩은 자원을 구글에서 사용하기 때문에 위에서 파싱한 결과물을 코랩에 드래그 앤 드랍 해주어야 합니다. 이때, 파싱한 웹에 따라서 인코딩 방식이 다를 수 있는데 확인하고 주석의 3가지 중 하나로 인코딩해줍니다.

<img src='https://github.com/HwanGonJang/HwanGonJang.github.io/blob/master/Pictures/w_2.png?raw=true'>

이제 형태소 분석 라이브러리를 설치해줍니다.
~~~python
!apt-get install g++ openjdk-7-jdk
!apt-get install python3-dev; pip3 install konlpy
~~~

구글에서 자동으로 라이브러리를 설치하기 시작하고 금방 완료됩니다.

~~~python
from konlpy.tag import Twitter
twitter = Twitter()

twitter.morphs('안녕하세요 저는 장환곤이고 대학생입니다. 숭실대학교 학생입니다.')

morph_list = []
for i in txt:
  morphs = twitter.nouns(i)
  
  for morph in morphs:
    morph_list.append(morph)

len(morph_list)
~~~

라이브러리 설치가 완료되면 라이브러리를 불러오고 twitter 라는 함수를 활용해 형태소를 분석합니다. morphs라는 명령을 활용하면 매개변수의 스트링을 다음과 같이 형태소 단위로 나누어 줍니다. 같은 방식으로 morph_list 라는 배열을 만들어 for문을 활용해 형태소 단위로 저장 해줍니다. 후에 len함수로 확인해보면 배열에 잘 들어갔음을 확인 할 수 있습니다.

<img src='https://github.com/HwanGonJang/HwanGonJang.github.io/blob/master/Pictures/w_3.png?raw=true'>

이제 단어의 개수별로 함께 저장해주는 작업이 필요합니다. 워드클라우드는 단어의 빈도수가 많을 수록 그 단어를 크게 출력하기 때문에 구분이 필요합니다.

~~~python
from collections import Counter
count = Counter(morph_list)
count.most_common()
~~~

컬렉션 라이브러리의 카운터 함수를 가져와서 가공한 단어들을 개수별로 묶어줍니다.

<img src='https://github.com/HwanGonJang/HwanGonJang.github.io/blob/master/Pictures/w_4.png?raw=true'>


마지막으로 이렇게 정리한 단어들을 다시 for문을 활용해 텍스트 파일로 만들어서 다운로드 받아주면 데이터 가공 성공입니다.

~~~python
for i in morph_list:
  f = open('google.txt', 'a', encoding='utf8')
  f.write(i+'\n')
  f.close()
~~~

텍스트 파일로 가져올때는 utf-8 로 인코딩 해줘야 합니다.

<img src='https://github.com/HwanGonJang/HwanGonJang.github.io/blob/master/Pictures/w_5.png?raw=true'>


### 3. 워드클라우드 생성하기
이제 준비가 끝났습니다. 다시 주피처 노트북에서 새 파일을 만들어서 워드클라우드를 생성해줍니다. 먼저 워드클라우드 모양을 만들 사진을 하나 찾아서 다운받아야합니다. 이때 배경이 없는 사진이여야 합니다.

~~~python
import numpy as np
import os
import re
from PIL import Image
from os import path
from wordcloud import WordCloud
import matplotlib.pyplot as plt

f = open('data.txt', 'r', encoding='utf8')
txt = f.readlines()
f.close()

#줄바꿈, 콤마 제거
new_txt = []
for i in txt:
    new_txt.append(i.replace('\n', ''))

#제거한 데이터 합치기
str_txt = ''
for i in new_txt:
    str_txt = str_txt + i + ' '

#데이터 확인하기
from collections import Counter
Counter(new_txt).most_common()

#사용하지 않을 단어 설정
stop = ['.', '을', '', '이']
stopwords = set(stop)

#워드클라우드 생성, 매개변수 설정
wc = WordCloud(background_color='white', font_path = 'C:/Users/myggo/AppData/Local/Microsoft/Windows/Fonts/DXMSubtitlesM-KSCpc-EUC-H.ttf', max_words=1000, mask=mask, stopwords=stopwords, margin=10, random_state=1, contour_color='black').generate(str_txt) 
#저장될 워드클라우드 사진 이름
wc.to_file("WordCloud.png")
plt.axis("off")
plt.figure(figsize = (10, 10))
plt.imshow(wc, interpolation="bilinear")
plt.axis("off")
plt.show()
~~~

줄바꿈 부분은 주피터노트북에서 새 코드를 작업한 기준입니다. 위의 라이브러리들을 불러온 후 가공한 데이터 파일을 가져옵니다. 가공한 데이터 텍스트 파일에는 줄바꿈과 콤마가 그대로 적혀있으므로 for문을 통해 지워줍니다. 지워진 데이터를 다시 for문으로 공백과 함께 합쳐주면 다음과 같이 형태소와 공백으로 분리된 결과물이 나오게 됩니다. 

<img src='https://github.com/HwanGonJang/HwanGonJang.github.io/blob/master/Pictures/w_7.png?raw=true'>


마지막으로 stop이라는 변수에 워드클라우드로 나타내지 않을 단어들을 설정해주면 됩니다. 워드클라우드는 생성 함수는 주석을 확인해주세요.

위의 코드를 실행하면 다음과 같이 깨끗하고 예쁜 워드클라우드가 나타납니다!\

<img src='https://github.com/HwanGonJang/HwanGonJang.github.io/blob/master/Pictures/w_9.png?raw=true'>


<img src='https://github.com/HwanGonJang/WordCloud_Python/blob/main/Google2.png?raw=true'>



## 결과
이 이미지를 머그컵에 전사해줍니다. 이것으로 강의 준비를 마쳤습니다. 강의 준비를 명목으로 한 프로젝트이지만 또 한번 성장한 것 같습니다.

<img src='https://github.com/HwanGonJang/HwanGonJang.github.io/blob/master/Pictures/cup.jpg?raw=true'>
