---
title: "Express+MongoDB+GraphQL #1 MongoDB 만들기"
date: 2019-09-25 20:08:00 -0400
categories: 
---

* MongoDB 만들기
--

설치를 하지않고 몽고DB의 클라우드 서비스를 이용하도록 하겠습니다!!

로컬로 하시고 싶으신분은 따로 다운 받으셔서 진행하시면되겠습니다.

전 개인적으로 클라우드 서비스가 편한거같아요..!

#### 우선 mongoDB 공식홈페이지로 접속합니다.

mongoDB : https://www.mongodb.com/

![1.JPG](https://images.velog.io/post-images/kjs100184/62dca590-9cb2-11e9-af5e-5f0cc1d1216b/1.JPG)

자! 이렇게 홈페이지가 나오셨나요??
#### 이메일을 작성해주시고 버튼을 눌러주세요.



![2.JPG](https://images.velog.io/post-images/kjs100184/f01301c0-9cb2-11e9-b920-03647a5c6644/2.JPG)

#### 구축하기를 누릅니다.

![3.JPG](https://images.velog.io/post-images/kjs100184/5de604c0-9cb5-11e9-b920-03647a5c6644/3.JPG)


![4.JPG](https://images.velog.io/post-images/kjs100184/626439e0-9cb5-11e9-884a-afa930f1cd3b/4.JPG)

#### 마음에 드시는 클라우드 서비스와 리전을 골라주세요.
프리티어로 선택해주시면 됩니다!
전 대한민국에서 가까운(?) 곳으로 했습니다. 
어디를 선택하시든 상관없습니다.. 
네트워크 속도가 제한되어있기때문에 ㅠㅠ

![5.JPG](https://images.velog.io/post-images/kjs100184/67f146f0-9cb5-11e9-884a-afa930f1cd3b/5.JPG)

여기까지 잘 따라오고 있으신가요?
조금만 기다리면 클러스터가 생성이 됩니다.

#### 이제 왼쪽 하단에있는 시작하기를 따라가겠습니다. (2번째 버튼 클릭)

![6.JPG](https://images.velog.io/post-images/kjs100184/87698ae0-9cb7-11e9-884a-afa930f1cd3b/6.JPG)

#### 메세지가 뜬 버튼을 클릭해줍니다.

![7.JPG](https://images.velog.io/post-images/kjs100184/ba445760-9cb7-11e9-884a-afa930f1cd3b/7.JPG)

#### 그럼 새로운 유저를 만드는 창이 뜨는데요.
SCRAM은 MongoDB에서 사용하는 인증 방식입니다.
오른쪽 윗칸에 닉네임하고 패스워드를 작성해줍니다.
닉네임과 패스워드는 추후에 데이터베이스를 불러오는데 사용합니다.
User Privileges에서는 읽기와 쓰기를 모두 할 것이기때문에 두 번째 박스를 체크해주고 유저를 만듭니다.


![8.JPG](https://images.velog.io/post-images/kjs100184/3a477d70-9cb8-11e9-884a-afa930f1cd3b/8.JPG)

#### 새로운 유저가 만들어졌는지 확인합니다. 
#### Get Started 세번째 버튼을 누릅니다.


![9.JPG](https://images.velog.io/post-images/kjs100184/49880fc0-9cb8-11e9-884a-afa930f1cd3b/9.JPG)

#### 메세지 창이 뜬 버튼을 눌러주시구요.


![10.JPG](https://images.velog.io/post-images/kjs100184/66f49010-9cb8-11e9-87cc-f3f5cc308771/10.JPG)

#### 어느 IP에서 사용 할 것인지 정해주는겁니다.
전 어디서든 사용하고 싶으니.. 어디서나 액세스 허용!을 하겠습니다.


![11.JPG](https://images.velog.io/post-images/kjs100184/998979f0-9cb8-11e9-b920-03647a5c6644/11.JPG)

#### Get Started의 4번째 버튼인 샘플만들기는 건너뛰고 마지막 버튼을 눌러줍니다.
#### connect 버튼을 눌러주시면 아래와 같은 화면이 나옵니다.

![12.JPG](https://images.velog.io/post-images/kjs100184/d0e86f00-9cb8-11e9-87cc-f3f5cc308771/12.JPG)

#### 우린 애플리케이션에서 사용을 할 것이기 때문에 가운데 박스를 클릭해줍니다.

![13.JPG](https://images.velog.io/post-images/kjs100184/18b552d0-9cb9-11e9-87cc-f3f5cc308771/13.JPG)

#### Connection String Only 안에 있는 코드를 붙여주기만하면 MongoDB를 사용할 수 있습니다.
#### mongodb+srv://{user_name}:{password}@{id}...mongodb.net/{DatabaseSchema}?...
위에 있는 uri를 잠깐 설명하자면 ~~(uri는 오타가 아닙니다...)~~
여기서 중요한건 {password} 와 {DatabaseSchema} 입니다!
현재 "test"라고 적힌부분있죠? 여기를 변경해주면 저장하는 곳이 달라집니다. 현재는 "test"라는 Schema로 저장이 되겠죠?
스키마..는 그냥 테이블이라고 생각해주시면 됩니다.

#### 다음 글에서는 만들고 읽고 수정하고 삭제하기를 하겠습니다.
