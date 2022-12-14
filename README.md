# Capstone-Design1-2022-2
# 그래프 중심성을 활용한 프로야구 정규시즌 분석

## Summary
통계의 스포츠라고도 불리는 야구는 다양한 통계를 가지고 있다. 야구의 각 경기는 공격과 수비가 명확하게 구분되어 있어 다른 요인과 결합되지 않은 공격 기록, 수비 기록 및 투수의 기록을 추출할 수 있다. 해당 기록들 중 어느 역량이 가장 중요한지 파악하고자 해당 프로젝트에서는 Graph centrality를 사용하여 각 요인이 경기에 미치는 영향을 분석한다. 2022 년도 국내 프로야구 정규시즌의 720 경기 중 무승부인 12 경기를 제외한 708 경기에서, 각 경기와 해당 경기에서 발생한 요인, 경기 결과로 이어지는 그래프를 구성하여 Betweenness centrality, PageRank centrality 를 분석한 결과, 득점에 직접적으로 영향을 미치는 타자력과 연관된 요인들이 가장 높은 중심성을 보였고, 실점에 기여하는 투수력과 수비력 순의 중심성 결과를 도출하였다.

## Bulit with
* [selenium](https://selenium-python.readthedocs.io/)
* [networkx](https://networkx.org/)
* [sklearn](https://scikit-learn.org/stable/)

## Project description
### 1. 데이터 수집 및 전처리
selenium 라이브러리를 활용하여 야구 통계 사이트에서 2022년도 정규리그 720 경기의 박스스코어를 추출한다.<br>
추출된 raw data에 다음 전처리를 수행한다.
 - score를 기준으로 승리팀, 패배팀 결정
 - 비율형 데이터 수치형으로 변환
 - 각 팀 기록 합산
 - 경기와 무관한 columns 제거
 - 수치 정규화<br>


### 2. 그래프 구현
networkx와 matplotlib 라이브러리를 활용하여 그래프를 구현 및 시각화한다.<br>해
그래프는 정규화 이전 테이블과 정규화 이후 테이블로부터 다음과 같이 구현한다.<br>
1) 데이터 프레임의 각 인덱스를 노드(경기 번호)로 지정
2) 각 열의 이름을 주요 변수로써 그래프에 노드로 추가
3) 경기 결과 노드('win') 추가
4) 정규화 이전의 수치가 양수인 경우 변수를 정규화한 수치를 가중치로 가지는 단방향 엣지 추가
5) 정규화 이전의 수치가 음수인 경우 해당 경기와 요인 사이에 엣지를 가지지 않음
6) 각 요인은 동일한 가중치를 가지는 경기결과 노드로 향하는 단방향 엣지를 포함



모든 노드를 표시하여 구현한 그래프는 다음과 같다.
![all_nodes](https://user-images.githubusercontent.com/73116458/208377457-d7abf28a-874f-406f-8b25-b900da856aa6.png)

직관적 시각화를 위해 10개 경기에 대하여 구현한 그래프는 다음과 같다.
![10_nodes](https://user-images.githubusercontent.com/73116458/208377667-4614332f-8401-41f7-b5f3-b80229c05810.png)

### 3. 그래프 중심성 도출
networkx 내장 메소드를 활용하여 각 요인의 Betweenness, PageRank centrality를 추출하고, 그 서열을 확인한다.
![상위10](https://user-images.githubusercontent.com/73116458/208378957-00d8c75c-a350-4e73-8fd5-55b00b576201.JPG)

## Result
### 상관관계를 통한 graph centrality 비교분석
각 통계와 경기 승패가 가지는 상관계수는 주로 득점과 상관관계가 깊은 요인들이 높은 수치를 보인다. 출루율, 장타율, 홈런 등 득점을 낼 수 있는 타자기록 위주의 기록이 높은 순위를 차지하는 반면, 그래프 중심성 서열은 투수력, 투구 중 스트라이크 비율 등 경기를 승리로 이끈 직접적인 득점 이외의 요인도 추출할 수는 있었지만, 경기와 높은 연관성을 가지지만, 모든 경기에 등장하지 않아 결속력이 약한 홈런은 상대적으로 낮은 중심성 수치를 보인다.

### 기계학습 모델 변수 중요도와 graph centrality 비교분석
학습된 기계학습 모델에서의 변수중요도는 실책이나 평균 실책률, 볼넷 안타 허용 수와 같이 그래프 중심성에서 최하위에 위치한 실점에 기여하는 요인들 또한 중요한 요소로서 그 비중을 나타낸다. 그래프 중심성의 서열을 통해서는 득점에 기여하는 요인과 실책에 기여하는 요인을 구분할 수는 있지만 그 비율을 알 수 없다.

### 결과 정리
각 요인을 그래프 중심성 서열로 나타내었을 때, 알 수 있는 것은 다음과 같다.
- <b>해당 그래프에서의 그래프 중심성 서열으로는 승리하는 팀이 일반적으로 포함하는 변수의 서열을 알 수 있다.</b> 매 경기마다 등장하지 않는 홈런과 같은 변수는 상위권에 위치하지 않는다.
- <b>그래프 중심성을 통해 보았을 때, 일반적으로 경기의 승패에 연관있는 기록은 타자 기록에 속하는 경우가 가장 많았고, 그 다음으로 투수 기록, 수비 기록 순으로 나타났다.</b> 중심성 상위 10개 기록에서 7개가 타자 기록, 3개가 투수 기록, 하위 10개 기록에서 2개가 타자 기록, 5개가 투수 기록, 3개 수비 기록이 존재하며, 개수가 아닌
비율로 볼 때, 수비 기록 중 절반이상이 하위 10개 기록에 속하며, 승리와의 연관이 저조하나, 패배와 연관되어 있을 것으로 판단된다.
- <b>중심성을 반대로 구하여 각 변수가 패배에 기여한 정도를 구할 수 있으나, 두 중심성 수치를 결합하여 하나로 나타내는 것에는 어려움이 있다.</b> 머신러닝 결과와 같이 경기 결과를 통합하여 표현하기 어려운 구조의 그래프로 한 쪽의 그래프를 구할 수는 있지만, 두 그래프를 구하여 중심성을 구했을 때, 경기에서 변수의 중요도를 하나의 서열로 나타내기 어렵다.
