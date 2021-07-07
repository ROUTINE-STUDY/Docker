[##_Image|kage@WzcEI/btq84tlU3iu/2CkgDxh6QyXJgaMOKxjnPK/img.png|alignCenter|data-origin-width="1215" data-origin-height="726" data-ke-mobilestyle="widthOrigin"|||_##]

1.우선 노드 어플리케이션을 만들 디렉토리를 만듭니다.

[##_Image|kage@cuTExQ/btq8ZZ03CWA/xBt4ckkIk4VqSKJAnqoKR1/img.png|alignCenter|data-origin-width="469" data-origin-height="411" data-ke-mobilestyle="widthOrigin"|||_##]

2.Visual Studio code로 해당 디렉터리를 열고 터미널에 npm init을 입력하여 package.json을 만들어 줍니다.

npm init을 입력하면 여러 가지 입력하라는 문구가 나오는데

본 예제에서는 그냥 전부 엔터 치고 넘겼습니다.

[##_Image|kage@deiK8E/btq810dNQoA/ZkeJPqncjXmpBbVsZQHMP1/img.png|alignCenter|data-origin-width="1189" data-origin-height="935" data-ke-mobilestyle="widthOrigin"|||_##]

3.nodejs를 위한 설정을 입력합니다.

dependencies에 express 부분만 추가하면 됩니다.

express란? [https://velog.io/@madpotato1713/JAVASCRIPT-express%EB%9E%80](https://velog.io/@madpotato1713/JAVASCRIPT-express%EB%9E%80)

[##_Image|kage@cra6K5/btq84P3sL0v/vVky5UpKzjp1ZyQfuNRLKK/img.png|alignCenter|data-origin-width="562" data-origin-height="392" data-ke-mobilestyle="widthOrigin"|||_##]

4.server.js 파일을 추가해줍니다. 해당 파일은 express의 사용을 위해 필요한 파일입니다.

[##_Image|kage@Bb4jb/btq8Z06QJ2k/Xpsh4BGdNn2ZhEQwQuH5k0/img.png|alignCenter|data-origin-width="746" data-origin-height="361" data-ke-mobilestyle="widthOrigin"|||_##]

5.Dockerfile을 작성합니다.

baseImage는 저희는 노드를 운영할 것이므로 node:10

노드를 실행하는데에는 npm이 필요하므로 Run에는 npm install

컨테이너 시작 시 실행 될 명령어 "node"와 "server.js"를 입력해줍니다.

[##_Image|kage@EUxn8/btq8Zv0afYk/LN3pk4OrJyYo7s7871jTr1/img.png|alignCenter|data-origin-width="670" data-origin-height="339" data-ke-mobilestyle="widthOrigin"|||_##]

6.실행 흐름을 보면 아래와 같습니다.

baseLine인 node10에서 npm install로 필요한 파일들을 다운 받고,

저장된 명령어인 node, server.js를 실행합니다.

[##_Image|kage@tAFno/btq80oMX9Mm/AZqqgcfwckqXcHKSrBkYYk/img.png|alignCenter|data-origin-width="433" data-origin-height="373" data-ke-mobilestyle="widthOrigin"|||_##]

7.visual studio에서 docker build -t 유저네임/프로젝트네임:버전 ./(docker파일 위치)를 실행해줍니다.

그러나 막상 해당 문구를 실행하면 package.json이라는 파일이 없다면서 오류가 나는 것을 볼 수 있습니다.

[##_Image|kage@mNEyV/btq84fWcSOh/TGZTF4yQ0rQfGywSVgKs4K/img.png|alignCenter|data-origin-width="920" data-origin-height="483" data-ke-mobilestyle="widthOrigin"|||_##]

8.왜냐면 build가 된 것은 Dockerfile뿐이며, 나머지 server.js나 package.json 파일이 포함되지가 않았기 때문입니다.

따라서 npm install을 베이스라인인 node:10에서 실행하지만, npm install을 하기 위해선 package.json이 필요하므로, 오류가 발생하게 됩니다.

[##_Image|kage@lyhw7/btq80zuDnjn/vXJSCdSMlJ14FCqZ8m1tV1/img.png|alignCenter|data-origin-width="721" data-origin-height="282" data-ke-mobilestyle="widthOrigin"|||_##]

8.오류를 제거하기 위해 package.json 파일을 빌드할 때 복사하도록 설정합니다.

(코드에선 package-lock.json에 복사를 위해 package\*.json을 입력했지만 docker를 공부할 때 중요한 건 아니니 넘어갑니다.)

[##_Image|kage@cF1AzG/btq81TluQpC/kLIp4gJXdMicN5JBnLmDF1/img.png|alignCenter|data-origin-width="664" data-origin-height="292" data-ke-mobilestyle="widthOrigin"|||_##]

9.이제 다시 빌드를 위해 docker build 명령어를 사용합니다.

Dockerfile에 입력한 순서대로 node에서 package.json 파일을 복사한 후,

복사한 package.json 파일을 읽어 필요한 종속성들을 npm install로 다운 받습니다.

[##_Image|kage@zE7TU/btq81ZM6W9y/e6FOHPtNikyC54DDyhgxxK/img.png|alignCenter|data-origin-width="806" data-origin-height="502" data-ke-mobilestyle="widthOrigin"|||_##]

10.이제 방금 build한 이미지로 컨테이너를 생성하면,

지금까지 안나온 CMD 즉, 컨테이너를 실행할 때 실행되는 명령어들이 실행될 것입니다.

허나, 막상 실행해보면 원하는 오류가 뜹니다.

[##_Image|kage@S5smV/btq83jxR84M/rExAZfUdLcIYZSACrypwq1/img.png|alignCenter|data-origin-width="772" data-origin-height="227" data-ke-mobilestyle="widthOrigin"|||_##]

11.왜나하면 현재 이미지를 만드는데 사용 된 것을은 node10과 package.json파일입니다.

막상 서버를 실행하는데 필요한 server.js가 추가 되지 않았습니다.

따라서 Dockerfile에 COPY예 server.js를 추가해서 다시 이미지를 빌드합니다.

[##_Image|kage@yvEeg/btq84vR7L9f/P54WYSijgwZqPKOk4JJsj0/img.png|alignCenter|data-origin-width="701" data-origin-height="353" data-ke-mobilestyle="widthOrigin"|||_##][##_Image|kage@eS9QJI/btq84KnXgUG/KeKjDUj34WAsSHiMK8z4dk/img.png|alignCenter|data-origin-width="884" data-origin-height="521" data-ke-mobilestyle="widthOrigin"|||_##]

12.다시 만들어진 이미지를 실행하여, 컨테이너화 시켜줍니다.

밑에서 보기와 같이 실행이 되는 것을 볼 수 있습니다.

[##_Image|kage@dfMA49/btq85Cptwmv/aKxrxELsUtZ1ALeOciKJkk/img.png|alignCenter|data-origin-width="853" data-origin-height="323" data-ke-mobilestyle="widthOrigin"|||_##]

13.웹브라우저로 해당하는 포트로 접속하면 접속이 되지 않은 것을 볼 수 있습니다.

왜냐? run할 때 포트 맵핑을 안해줬기 때문입니다.

[##_Image|kage@ckCrd9/btq80yoYDVD/YcxpYFf9Co3UfCOCkcHKcK/img.png|alignCenter|data-origin-width="891" data-origin-height="475" data-ke-mobilestyle="widthOrigin"|||_##]

14.이를 수정하기 위해, 'docker run -p 로컬호스트:네트워크 이미지이름'을 다시 실행해줍니다.

[##_Image|kage@QoJAc/btq85gmEC9e/KZPkTxhJcT4NNBwT5aI1z1/img.png|alignCenter|data-origin-width="934" data-origin-height="93" data-ke-mobilestyle="widthOrigin"|||_##]

15.로컬호스트(http://localhost:49160/)로 접속을 하면, server.js에서 /로 요청이 들어왔을 때, 반환하기로 한 문자열이 출력이 되는 것을 볼 수 있습니다.

[##_Image|kage@4UbGG/btq83jSaCMq/9tmV148WCwVswUSSeWtvvK/img.png|alignCenter|data-origin-width="988" data-origin-height="247" data-ke-mobilestyle="widthOrigin"|||_##]

16.working directory를 설정해줍니다.

working directory를 설정해주는 이유는

현재 실행 중인 컨테이너에 들어가 내부 디렉터리 구조를 보면 아래와 같이 되어 있는 것을 볼 수 있습니다.

node와 실행 중인 어플리케이션이 컨테이너에 최상위층에 모두 저장 되어있는 것을 볼 수 있습니다.

[##_Image|kage@cjZQry/btq81ZTZh4V/JRaAmmRKKnleUnVl5RBbBk/img.png|alignCenter|data-origin-width="745" data-origin-height="84" data-ke-mobilestyle="widthOrigin"|||_##]

이와 같은 현상을 방지하기 위해 워킹 디렉터리를 따로 설정하여, 디렉터리 구조를 분리할 수 있습니다.

17.Dockerfile에 워킹디렉토리 설정하기.

[##_Image|kage@bFta3A/btq85UwLbC1/goyBICHSuCmf5D4qmKol3K/img.png|alignCenter|data-origin-width="630" data-origin-height="427" data-ke-mobilestyle="widthOrigin"|||_##]

18\. 다시 빌드해서 

[##_Image|kage@dDlx29/btq84fot5vB/hkcE4m6LxpcRfQI7uD5ys0/img.png|alignCenter|data-origin-width="820" data-origin-height="79" data-ke-mobilestyle="widthOrigin"|||_##]

실행 한 후에, 

[##_Image|kage@MLduT/btq84KBxDMr/LJVQ5oWw6DmunTwyg14xL0/img.png|alignCenter|data-origin-width="914" data-origin-height="78" data-ke-mobilestyle="widthOrigin"|||_##]

exec(실행 중인 컨테이너에서 명령어 실행하기)로 sh를 사용하여, 컨테이너 내부에서 쉘 실행합니다.

현재 위치를 pwd로 보면 워킹디렉터리로 설정 되어 있고, ls로 내부 목록을 보면, 프로젝트의 구성요소가 모두 들어가있는 것을 볼 수 있습니다.

[##_Image|kage@wrEfl/btq811qIV5K/FsLCGmq8TyPnxO2fHAKYT0/img.png|alignCenter|data-origin-width="697" data-origin-height="158" data-ke-mobilestyle="widthOrigin"|||_##]

Docker Volume에 대해

Docker Volume은 현재 프로젝트를 빌드할 때 일일이 파일들을 COPY해서 이미지를 빌드했지만, 

Docker Volume을 사용하면 로컬 저장소, 즉 현재 실행하고 있는 컴퓨터에 하드디스크에 있는 소스를 참조하여 컨테이너를 실행하는 것입니다.

밑에 이미지에서 위에 COPY가 우리가 지금까지 진행한 방법이고

볼륨이 아래와 같이 로컬에 있는 소스를 참조하는 방법입니다.

[##_Image|kage@LRDss/btq86fOmByB/S8mSwMylJluCRFFJZaNARk/img.png|alignCenter|data-origin-width="738" data-origin-height="744" data-ke-mobilestyle="widthOrigin"|||_##]

docker의 volume은 run에서 -v 플래그를 사용합니다.

docker run -p 5000:8080 -v /usr/src/app/node\_modules -v $(pwd):/usr/src/app username/projectname:version3 

첫번째 -v의 인자인 /usr/src/app/node\_modules는 로컬에 저장되어 있지 않으므로 참고할 대상이 없습니다. 그러므로 해당 요소는 참고하지 말라는 뜻입니다.

두번째 -v의 인자인 $(pwd):/usr/src/app가 있는데,

여기서 $(pwd)는 현재 로컬의 소스를 가리키며, /usr/src/app이 $(pwd)를 참조하라는 뜻으로 사용됩니다.

마지막의 username/projectname:version3은 컨테이너화할 이미지를 뜻합니다.

[##_Image|kage@ttzlb/btq81dEZM9e/Lj5JW7SX7pArdId5l7AUXk/img.png|alignCenter|data-origin-width="1214" data-origin-height="71" data-ke-mobilestyle="widthOrigin"|||_##]

이제 다시 접속을 해보면. 밑에와 같이 접속이 됩니다.

[##_Image|kage@cPGUoY/btq83jkrrn4/hhdidUUKKikPcmaTVklR9K/img.png|alignCenter|data-origin-width="766" data-origin-height="248" data-ke-mobilestyle="widthOrigin"|||_##]

이 상태에서 server.js에 들어가서 res.send에 문자열을 바꿔줍니다.

현재 상황에선 localhost:5000으로 다시 들어가도 문자열이 바뀌지 않습니다.

[##_Image|kage@VWs5Q/btq84suc4g2/AGzTP95nzjndsMRBghajk1/img.png|alignCenter|data-origin-width="692" data-origin-height="412" data-ke-mobilestyle="widthOrigin"|||_##]

그러나 컨테이너를 다시 실행하면

아래와 같이 이미지를 다시 빌드하지 않아도 코드의 내용이 수정되는 것을 알 수 있습니다.

이젠, 매 번 이미지를 빌드하지 않고, volume 옵션을 준 후, 컨테이너를 재실행하면 됩니다!

[##_Image|kage@xWolr/btq80o7r11n/cqzKBUBCecXvKOMhDEamyk/img.png|alignCenter|data-origin-width="1026" data-origin-height="254" data-ke-mobilestyle="widthOrigin"|||_##]
