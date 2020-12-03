---
layout : post
title : "[Portfolio management] 1. 데이터셋 구성하기"
date : "2020-11-25 14:53:00 +0530"
category : Project
comments : true
---

## **Introduction**

앞으로 강화학습을 이용해서 한국 KOSPI 시장의 주식들을 대상으로 포트폴리오를 구성해보는 프로젝트를 진행해보려고 합니다.
크게 **1)데이터셋 구성 2)기존 투자기법 구현 3)RL 기반 구현** 이렇게 3단계로 실습해볼 것이고,
가능하다면 논문 리뷰도 추가할 예정입니다.

자세한 사항은 [github page](https://github.com/hanseul-jeong/Portfolios "내 깃헙")를 참고해주세요!  
(혹시 도움이 되셨다면 STAR! +_+)   


좀 더 구체적으로는 대신증권 API를 이용해서 **KOSPI**시장의 데이터셋을 구축하고,
Python 및 Pytorch를 이용한 간단한 **Multi-Layer Perceptron (MLP)** 모델을 구성하려고 합니다.
비교대상이 될 기존의 투자기법들은 (앞으로 공부를 해야겠지만), 
**1) Buy-and-Hold** : 시장 가격으로 KOSPI 지수에 해당. 
**2) OLMAR 3) RMR 4) Anticor 5) WMAMR**  
입니다.

모델 및 비교대상들은 앞으로의 포스팅에서 더욱 자세히 다루도록 하겠습니다.

## **Collect Dataset**

대신증권이 종목 리스트 조회 및 데이터를 추출하기 위한 API가 굉장히 잘 되어있기 때문에  
이를 이용해서 데이터를 수집해보도록 하겠습니다.

API를 연결하기 위해서는 대신증권의 계좌가 필요하고 creon-plus 프로그램에 로그인되어있어야합니다.
비대면으로도 계좌계설이 가능하기때문에 [크레온 홈페이지-비대면 계좌개설](http://money2.creontrade.com/E5/WTS/Customer/AccountOpen/DW_NoFaceOpen.aspx?m=7108&p=6662&v=5573) 다음의 링크를 따라서 쉽게 신청하실 수 있고,  
Creon-Plus 프로그램 또한 [크레온 홈페이지-비대면 계좌개설](http://money2.creontrade.com/e5/mboard/ptype_basic/plusPDS/DW_Basic_Read.aspx?boardseq=299&seq=35&page=1&searchString=&prd=&lang=8&p=8833&v=8639&m=9505) 해당 링크 따라서 쉽게 설치 가능합니다.
   
위의 준비가 다 되었다면, 이제 환경설정이 필요합니다.  
대신증권의 프로그램이 32bit 기반이기 때문에 Python 32bit를 이용해야합니다.
저는 Anaconda가 64bit이기 때문에 가상환경의 python을 32bit로 생성하였습니다.

Anaconda prompt에서 다음의 코드를 이용해서 python 3.6버전의 가상환경을 생성해줍니다.

    set CONDA_FORCE_32BIT=1
    conda create -n win32 python=3.6 
    conda activate win32

"python" 명령어를 통해 version을 확인합니다.   

    python      
    
![Image window32bit](/assets/img/window32bit.png)

코드자체는 Open source로 많이 나와있는데 그 중에서도 GUI로 깔끔히 만들어두신 코드가 있어서,  
[이 코드](https://github.com/gyusu/Creon-Datareader "대신증권 데이터 수집 github")를 이용했습니다.  

필요한 라이브러리들은 numpy, pyqt, sqlite, pywin32, pandas 가 필요합니다.  

    conda install pyqt sqlite pywin32 pandas numpy=1.16.1
    
다음의 명령어로 설치해주세요. (numpy 버전에 따라 오류가 있는 것 같아서 버전 명시했습니다.)  
- 저는 원래 IDE로 pycharm을 이용하고 pycharm에서 바로 설치하는 방식을 주로 이용하는데,  
32bit로 설정해서 그런지 이 방식으로는 아래 그림과 같은 오류가 발생했습니다.  
anaconda prompt나 pycharm의 terminal을 이용하세요!  

![Image HTTPerror](/assets/img/HTTPerror.png)  

위 코드의 결과물로는 확장자는 .db 파일이고 KOSPI, KOSDAQ의 전 종목의 일간 시가, 고가, 저가, 종가, 거래량 데이터를 받아올 수 있습니다.      
저는 KOSPI만 대상으로 하였고, 20년 11월 25일 기준으로 총 1563종목의 데이터 수집을 완료하였습니다!  

이후에는 수집한 데이터의 날짜 및 종목별로 데이터 pre-processing을 거칠 예정입니다.
감사합니다. :)  

 
   
    