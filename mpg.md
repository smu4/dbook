# 헬로 데이터 과학: 탐색적 데이터 분석
Jin Young Kim  
#### 개별 속성 분석하기

이번에는 R로 탐색적 분석의 과정을 살펴보자. 우선 2장에서 설명한대로 GitHub에서 다운받은 파일이 있는 곳으로 작업 디렉토리를 설정하자. (단축키: Ctrl+Shift+H) 그 후 이 책에서 제공하는 R 라이브러리를 로딩하고, 이 장에서 사용할 패키지를 불러오자.



```r
source("dbook.R")
load.packages(c("dplyr", "ggplot2", "tidyr", "knitr"))
```

```
## [1] loading dplyr
## [1] loading ggplot2
## [1] loading tidyr
## [1] loading knitr
```

```r
mpg = read.table("mpg.txt", header=T, sep="\t")
```

아래는 우리가 사용할 MPG 데이터에서 10건의 표본을 추출한 것이다. dplyr 패키지에서 제공하는 sample_n() 함수는 주어진 데이터에서 n개의 표본을 추출한다. knitr 패키지에서 제공하는 kable() 함수는 테이블을 보기좋게 출력해준다. 표본 데이터를 눈으로 보면서 데이터에 익숙해지도록 하자. 


```r
kable(sample_n(mpg,10))
```

      manufacturer   model                trans        drv    displ   year   cyl   cty   hwy  fl   class      
----  -------------  -------------------  -----------  ----  ------  -----  ----  ----  ----  ---  -----------
99    audi           a4 quattro           manual(m6)   4        3.1   2008     6    15    25  p    compact    
80    subaru         impreza awd          auto(l4)     4        2.5   1999     4    19    26  r    subcompact 
194   jeep           grand cherokee 4wd   auto(l5)     4        4.7   2008     8    14    19  r    suv        
16    ford           f150 pickup 4wd      auto(l4)     4        4.2   1999     6    14    17  r    pickup     
18    chevrolet      malibu               auto(s6)     f        3.6   2008     6    17    26  r    midsize    
229   hyundai        sonata               manual(m5)   f        2.5   1999     6    18    26  r    midsize    
174   toyota         camry solara         manual(m5)   f        3.0   1999     6    18    26  r    compact    
137   dodge          durango 4wd          auto(l5)     4        4.7   2008     8    13    17  r    suv        
93    dodge          dakota pickup 4wd    auto(l4)     4        5.2   1999     8    11    15  r    pickup     
112   chevrolet      malibu               auto(l4)     f        3.5   2008     6    18    29  r    midsize    

데이터의 값과 함께 각 속성의 데이터형도 살펴보자. R에서는 str()함수를 사용하여 주어진 데이터의 자료형을 확인할 수 있다. R에서 Factor는 카테고리형 데이터를 가리키며 levels는 각 속성에 존재하는 카테고리의 개수를 가리킨다. int나 num은 정수 및 실수형 데이터를 가리킨다.


```r
str(mpg)
```

```
## 'data.frame':	234 obs. of  11 variables:
##  $ manufacturer: Factor w/ 15 levels "audi","chevrolet",..: 3 4 15 11 15 15 8 14 4 15 ...
##  $ model       : Factor w/ 38 levels "4runner 4wd",..: 14 17 21 5 21 21 35 7 29 21 ...
##  $ trans       : Factor w/ 10 levels "auto(av)","auto(l3)",..: 3 9 3 9 8 9 8 4 4 10 ...
##  $ drv         : Factor w/ 3 levels "4","f","r": 1 1 2 2 2 2 1 2 3 2 ...
##  $ displ       : num  3.9 4.6 2 2.4 2 2 4.2 2.4 4 2 ...
##  $ year        : int  1999 1999 1999 1999 2008 1999 2008 2008 2008 2008 ...
##  $ cyl         : int  6 8 4 4 4 4 8 4 6 4 ...
##  $ cty         : int  13 13 19 21 22 21 12 21 16 21 ...
##  $ hwy         : int  17 16 26 29 29 29 18 31 24 29 ...
##  $ fl          : Factor w/ 5 levels "c","d","e","p",..: 5 5 5 5 4 5 5 5 5 4 ...
##  $ class       : Factor w/ 7 levels "2seater","compact",..: 7 5 2 2 2 2 7 3 6 2 ...
```

개별 데이터를 살펴본 후에는 summary() 함수를 통해 데이터를 요약하는 다양한 통계값을 계산하여 살펴볼 수 있다. 이런 통계값은 데이터의 유형에 따라 달라진다. 예컨대 연속된 수치에 대해서는 평균 및 중간값 등을, 그리고 범주 및 순서를 나타내는 속성에 대해서는 최빈값을 계산할 수 있을 것이다. 아래는 MPG 데이터의 다양한 속성들의 통계값이다.


```r
summary(mpg)
```

```
##      manufacturer                 model            trans    drv    
##  dodge     :37    caravan 2wd        : 11   auto(l4)  :83   4:103  
##  toyota    :34    ram 1500 pickup 4wd: 10   manual(m5):58   f:106  
##  volkswagen:27    civic              :  9   auto(l5)  :39   r: 25  
##  ford      :25    dakota pickup 4wd  :  9   manual(m6):19          
##  chevrolet :19    jetta              :  9   auto(s6)  :16          
##  audi      :18    mustang            :  9   auto(l6)  : 6          
##  (Other)   :74    (Other)            :177   (Other)   :13          
##      displ            year           cyl             cty       
##  Min.   :1.600   Min.   :1999   Min.   :4.000   Min.   : 9.00  
##  1st Qu.:2.400   1st Qu.:1999   1st Qu.:4.000   1st Qu.:14.00  
##  Median :3.300   Median :2004   Median :6.000   Median :17.00  
##  Mean   :3.472   Mean   :2004   Mean   :5.889   Mean   :16.86  
##  3rd Qu.:4.600   3rd Qu.:2008   3rd Qu.:8.000   3rd Qu.:19.00  
##  Max.   :7.000   Max.   :2008   Max.   :8.000   Max.   :35.00  
##                                                                
##       hwy        fl             class   
##  Min.   :12.00   c:  1   2seater   : 5  
##  1st Qu.:18.00   d:  5   compact   :47  
##  Median :24.00   e:  8   midsize   :41  
##  Mean   :23.44   p: 52   minivan   :11  
##  3rd Qu.:27.00   r:168   pickup    :33  
##  Max.   :44.00           subcompact:35  
##                          suv       :62
```

데이터에 적절한 시각화를 적용하여 전체 값의 분포를 한눈에 볼 수 있다. 아래는 MPG 데이터셋의 고속도로 연비(hwy)를 확률 밀도 그래프(density plot), 히스토그램(histogram), 그리고 점플롯(dotplot)으로 표현한 것이다. 이 시각화 결과에 따르면 데이터에 크게 두 가지 그룹의 차량이 (세단과 SUV) 있다는 사실이 드러난다. 이중 히스토그램과 점플롯에서는 개별 데이터의 분포를 좀 더 뚜렷이 볼 수 있다.


```r
a = ggplot(mpg, aes(hwy)) # 기본 플롯을 만듦
p1 = a + geom_density()   # 기본 플롯을 확률밀도함수 형태로 출력
p2 = a + geom_histogram() # 기본 플롯을 히스토그램 형태로 출력
p3 = a + geom_dotplot()   # 기본 플롯을 점플롯 형태로 출력
multiplot(p1, p2, p3, cols=3)
```

![](mpg_files/figure-html/unnamed-chunk-5-1.png) 

#### 속성 간의  관계 분석하기

이제 속성 간의  관계를 분석하는 과정을 MPG 데이터를 통해 알아보도록 하자. 우선 아래 표는 수치형 속성 간의  상관계수를 나타낸 것이다. 연비(hwy/cty)와 실린더 수 (cyl) 및 배기량(disp)이 높은 상관 관계를 가지는 것을 볼 수 있다. 


```r
mpg.num = select(mpg, displ, year, cyl, cty, hwy)
round(cor(mpg.num), 3)
```

```
##        displ   year    cyl    cty    hwy
## displ  1.000  0.148  0.930 -0.799 -0.766
## year   0.148  1.000  0.122 -0.037  0.002
## cyl    0.930  0.122  1.000 -0.806 -0.762
## cty   -0.799 -0.037 -0.806  1.000  0.956
## hwy   -0.766  0.002 -0.762  0.956  1.000
```

하지만 상관계수가 데이터에 존재하는 모든 트렌드를 요약하는 것은 아니다. 같은 데이터에 해당하는 아래 스케터플롯을 보면 각 속성 쌍의 관계가 다양한 양상의 띈다는 것을 알 수 있다.


```r
plot(mpg.num)
```

![](mpg_files/figure-html/unnamed-chunk-7-1.png) 

이렇게 모든 속성 간의  관계를 상관도와 스케터플롯을 통해 관찰한 후에는 관심이 가는 개별 속성 간의  관계를 자세히 살펴보아야 할 것이다. 두 속성 간의  관계를 알아보는 방법은 대상 속성의 유형의 조합에 따라 달라진다. 우선 두 카테고리형 속성 간의  관계를 생각해보자. 두 속성이 모두 제한된 카테고리의 값을 갖기 때문에, 이를 분석하는 데에는 각 카테고리의 조합에 속하는 항목의 수를 나타내는 교차 테이블이나 모자이크 플롯이 적절하다.

다음 표와 모자이크 플롯은 MPG 데이터셋에서 실린더 수와 구동방식의 관계를 나타낸다. 후륜 방식은 대부분 대형차에, 그리고 전륜 방식은 소/중형차에 사용되는 것을 알 수 있다. 같은 데이터를 모자이크 플롯으로 보면 대부분의 차가 사륜 구동 혹은 전륜 구동을 띄고 있다는 사실, 그리고 실린더 수의 분포가 구동방식에 따라 다르다는 사실을 한눈에 알 수 있다.


```r
# cyl과 drv 속성의 교차 테이블을만든다.
table(mpg$cyl, mpg$drv)
```

```
##    
##      4  f  r
##   4 23 58  0
##   5  0  4  0
##   6 32 43  4
##   8 48  1 21
```

```r
mosaicplot(table(mpg$drv, mpg$cyl), cex=1.2, main="")
```

![](mpg_files/figure-html/unnamed-chunk-8-1.png) 

다음으로 카테고리형 속성과 수치형 속성 간의  관계를 분석하는 경우에는 제한된 종류의 값을 갖는 카테고리형 속성의 특성을 고려하여 각 카테고리별 데이터의 분포를 볼 수 있다. 여기에는 박스플롯이 널리 사용되는데, 박스플롯은 주어진 데이터의 25% 및 75%에 해당하는 백분위 값을 박스로 표시하고, 여기에 50%에 해당하는 중앙값을 가운데 굵은 선으로 표시한 플롯이다.

데이터를 대표하는 통계값을 한눈에 보기 위해 박스플롯을 사용한다면, 개별 데이터 값을 관찰하기 위해서는 스케터플롯을 사용한다. 단, 이 경우 jitter() 함수를 사용하여 같은 카테고리를 갖는 값들이 서로 겹치지 않도록 노이즈를 더해준다.  아래 플롯은 실린더 수(cyl)와 고속도로 연비(hwy)의 상관 관계를 보여준다. 



```r
par(mfrow=c(1,2))
plot(jitter(mpg$cyl), mpg$hwy, xlab="Year", ylab="MPG(hwy)")
boxplot(hwy ~ cyl, filter(mpg, cyl != 5), xlab="Year", ylab="MPG(hwy)")
```

![](mpg_files/figure-html/unnamed-chunk-9-1.png) 

또한 두 수치형 속성 간의  관계는 스케터플롯으로 확인하는 것이 가장 일반적이다. 아래 왼쪽의 스케터플롯에서는 배기량과 도시 연비의 상관 관계를 보여준다(상관계수:-0.799). 배기량과 도시 연비가 대체로 음의 상관 관계를 갖지만, 어느 정도 배기량이 높아지면 상관 관계가 희박해진다. 이에 반해 도시 연비와 고속도로 연비에 관한 아래 오른쪽의 스케터플롯에서는 대부분의 값들이 좀 더 추세선 근처에 모여 있는 것을 알 수 있다(상관계수:0.956).


```r
p1 = ggplot(mpg, aes(hwy, cty)) + geom_point() + geom_smooth()
p2 = ggplot(mpg, aes(displ, cty)) + geom_point() + geom_smooth()
multiplot(p2,p1, cols=2)
```

![](mpg_files/figure-html/unnamed-chunk-10-1.png) 

탐색적 분석에서는 두 개 이상의 속성 간의  관계를 동시에 보고 싶은 경우도 생긴다. 이럴 때는 기존의 스케터플롯이 유용한데, X와 Y의 위치에 두 가지 속성의 관계를 표시하는 스케터플롯의 각 점에 색상, 크기, 레이블 등의 다양한 속성을 추가할 수 있기 때문이다.

다시 MPG 데이터셋을 살펴보면 아래 차트에는 고속도로 연비(hwy)를 X축, 도시 주행시 연비(cty)를 Y축으로 하고, 여기에 배기량(disp)을 점의 크기에, 그리고 실린더의 개수(cyl)를 점의 색상에 적용했다. 아래 그래프를 보면 크고 밝은 점이 (실린더 수가 많고 배기량이 높은 차량) 그래프 좌측 하단에 몰려있는 것을 볼 수 있다. 이처럼 스케터플롯을 사용해 네 가지 속성의 관계를 동시에 확인할 수 있다.


```r
ggplot(mpg, aes(hwy, cty)) + 
	geom_point(aes(color = cyl, size = displ))
```

![](mpg_files/figure-html/unnamed-chunk-11-1.png) 

마지막으로 산포도 그래프에 텍스트 레이블을 적용한 사례를 살펴보자. 아래 그래프는 점 대신 각 차량 모델의 연비를 그래프에 나타낸다. 이때 레이블의 색상으로 제조사를 구분할 수 있도록 하였다. 


```r
# 메이저 제조사의 목록을 구하기
majors = mpg %>% 
	group_by() %>%      # 데이터를 제조사 기준으로 그룹하여
	summarize(count=n()) %>% # 제조사별 모델의 개수를 구하고
	filter(count > 10)       # 적어도 10대 이상의 모델이 있는 제조사만 남긴다

# 메이저 제조사의 모델을 구하기
mpg.majors = mpg %>% 
	filter(manufacturer %in% majors$manufacturer) %>% # 메이저 제조사의 차량만 남기고
	distinct(model)                     # 차량별로 한대씩만 남긴다.

# 모델별 연비에 모델 이름을 추가한 플롯
ggplot(mpg.majors, aes(hwy, cty)) + 			            # 기본 플롯에
	geom_text(aes(label = model, color = manufacturer), size = 4,  # 텍스트 레이블을 더하고
			  position=position_jitter(width=1, height=2))  # 레이블이 겹치지 않도록 노이즈를 더한다.
```

![](mpg_files/figure-html/unnamed-chunk-12-1.png) 

위 플롯을 보면 연비가 낮은 대부분의 차량은 4륜 구동의 SUV 및 트럭임을 알 수 있다. 또한 높은 연비를 자랑하는 차량은 대부분 한국/일본산이거나 그리고 독일의 폭스바겐에서 만들어진 것들이다. 또한 독일 아우디에서 만든 차량은 고속도로 연비에 비해 도시 연비가 좋지 않다는 것도 알 수 있다.

