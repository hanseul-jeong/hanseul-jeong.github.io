---
layout : post
title :  "[Portfolio Management] 1.5 데이터 preprocessing"
date :   '2020-12-10 16:46:36 +0530'
categories : Project
use_math : true  
comments : true  
---

강화학습을 이용해서 한국 KOSPI 시장의 주식들을 대상으로 포트폴리오를 구성해보는 프로젝트 입니다.
크게 **1)데이터셋 구성 2)기존 투자기법 구현 3)RL 기반 구현** 과정 중에서 이 포스팅은 1)의 후반에 해당합니다.  

[이전 글](https://hanseul-jeong.github.io/project/2020/11/25/Portfolio-Management-%EB%8D%B0%EC%9D%B4%ED%84%B0%EC%85%8B-%EA%B5%AC%EC%84%B1%ED%95%98%EA%B8%B0.html, "데이터셋 구성")에 이어서 구성한 데이터셋을 기간 및 회사 종류별로 Pre-processing 과정을 담아보았습니다.   

자세한 사항은 [github page](https://github.com/hanseul-jeong/Portfolios "내 깃헙")를 참고해주세요!  
(혹시 도움이 되셨다면 STAR! +_+)


> ## Pre-processing

데이터셋의 확장자는 db파일으로 sql 구문으로 쉽게 접근이 가능합니다.    

- 사실 지금까지 저는 csv 파일을 주로 다뤘어서 Pandas 라이브러리를 이용해서 처리하는 방식을 이용했는데,  
    이번 기회에 Python으로도 sql을 이용할 수 있다는 걸 알게 되었습니다..ㅎㅎ  

excel 프로그램으로 데이터를 쉽게 확인할 수 있는 csv파일과는 달리 db파일은 뷰어 프로그램이 별도로 필요합니다.  
데이터를 확인해보기 위해서 [DB browser for SQL](https://sqlitebrowser.org/dl/) 프로그램을 이용하였습니다.  
위 링크에서 사용하시는 사양에 맞는 버전의 프로그램을 다운받으시면 쉽게 설치 가능합니다.    

![Image DBbrowser](/assets/img/dbbrowser.png)  

Preprocessing의 주 목적은 원하는 **회사 종류**, **날짜** 를 선택하기 위함입니다.  
위 프로그램으로 확인한 결과, 각 회사마다 회사 코드를 테이블 명으로 하는 방식으로 DB가 구성되어있었습니다. (e.g., A005930 -> 삼성전자)

sqlite3 라이브러리를 이용해서, 'KOSPI.db' 파일에 다음과 같은 간단한 명령어로 접근할 수 있습니다.  

    import sqlite3
    data_path = 'KOSPI.db'
    StartDate = 20100101
    EndDate = 20200101
    
    conn = sqlite3.connect(data_path)
    cursor = conn.cursor()
    
    sql = "Select * from {code} where date >= {StartDate} and date <= {EndDate}".format(code=code, StartDate=StartDate, EndDate=EndDate)
    cursor.execute(sql)
    d = cursor.fetchall()

위의 코드에서 **code**를 설정해주어야하는데, 관심있는 다음의 16개 회사에 대한 데이터를 불러와보도록 하겠습니다.

    Assets = ['두산', '기아차', '한화', '대림건설', '롯데푸드', '한진', '금호산업', '대한항공',
                  'LG', '신세계', '농심', '삼성전자', '오뚜기', 'S-Oil', '금호타이어', '셀트리온']   

db파일과 함께 여러 회사의 list파일 (code_dict.csv) 이 존재하기 때문에 이 파일의 각 column을 'Code'와 'Name'으로 설정해줍니다. 
- 저는 엑셀에서 직접 다음과 같이 바꿔주었습니다.  

![Image dictExample](/assets/img/dict_example.PNG)  

다음의 dictionary를 통해 쉽게 회사명을 code로 변환할 수 있습니다.    

    import pandas as pd
    
    df = pd.read_csv('code_dict.csv')
    codeList = df['Code'].tolist()
    nameList = df['Name'].tolist()
    self.name_to_code = {name: code for code, name in zip(codeList, nameList)}

전체 코드는 다음과 같습니다.  

    # load data
    rawData = []
    conn = sqlite3.connect(data_path)
    cursor = conn.cursor()
    
    Assets = ['두산', '기아차', '한화', '대림건설', '롯데푸드', '한진', '금호산업', '대한항공',
              'LG', '신세계', '농심', '삼성전자', '오뚜기', 'S-Oil', '금호타이어', '셀트리온'] 
    
    codeList = [self.name_to_code[name] for name in Assets]
    for code in codeList:
        sql = "Select * from {code} where date >= {StartDate} and date <= {EndDate}"\
            .format(code=code, StartDate=StartDate, EndDate=EndDate)
        cursor.execute(sql)
        d = cursor.fetchall()
        rawData.append(d)
        
    conn.close()


이로 데이터 수집은 마무리되었습니다. 짝짝짝!!!   

다음에는 NN으로 구성한 RL 알고리즘의 성능을 비교하기 위해서 기존의 모멘텀 (Momentum) 기법들을 구현하고, 각 성능을 보여드리도록 하겠습니다.  
감사합니다. :)  
