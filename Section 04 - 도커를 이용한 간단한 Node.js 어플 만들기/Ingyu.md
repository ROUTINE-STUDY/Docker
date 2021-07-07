![image](https://user-images.githubusercontent.com/68156400/124755879-da79b680-df66-11eb-985f-bc76fc614a28.png)

1.우선 노드 어플리케이션을 만들 디렉토리를 만듭니다.

![image](https://user-images.githubusercontent.com/68156400/124755899-e2d1f180-df66-11eb-9745-b7eaa340a7c0.png)

2.Visual Studio code로 해당 디렉터리를 열고 터미널에 npm init을 입력하여 package.json을 만들어 줍니다.

npm init을 입력하면 여러 가지 입력하라는 문구가 나오는데

본 예제에서는 그냥 전부 엔터 치고 넘겼습니다.

![image](https://user-images.githubusercontent.com/68156400/124755939-ee251d00-df66-11eb-99a7-f503e8ef3df9.png)


3.nodejs를 위한 설정을 입력합니다.

dependencies에 express 부분만 추가하면 됩니다.

express란? [https://velog.io/@madpotato1713/JAVASCRIPT-express%EB%9E%80](https://velog.io/@madpotato1713/JAVASCRIPT-express%EB%9E%80)

![image](https://user-images.githubusercontent.com/68156400/124755986-f54c2b00-df66-11eb-83b4-c9028dedbd60.png)

4.server.js 파일을 추가해줍니다. 해당 파일은 express의 사용을 위해 필요한 파일입니다.

![image](https://user-images.githubusercontent.com/68156400/124756025-fe3cfc80-df66-11eb-9d8c-bb976f9641ed.png)

5.Dockerfile을 작성합니다.

baseImage는 저희는 노드를 운영할 것이므로 node:10

노드를 실행하는데에는 npm이 필요하므로 Run에는 npm install

컨테이너 시작 시 실행 될 명령어 "node"와 "server.js"를 입력해줍니다.

![image](https://user-images.githubusercontent.com/68156400/124756041-039a4700-df67-11eb-87eb-e6d30753817f.png)

6.실행 흐름을 보면 아래와 같습니다.

baseLine인 node10에서 npm install로 필요한 파일들을 다운 받고,

저장된 명령어인 node, server.js를 실행합니다.

![image](https://user-images.githubusercontent.com/68156400/124756062-0a28be80-df67-11eb-8530-d0479ef77c3c.png)

7.visual studio에서 docker build -t 유저네임/프로젝트네임:버전 ./(docker파일 위치)를 실행해줍니다.

그러나 막상 해당 문구를 실행하면 package.json이라는 파일이 없다면서 오류가 나는 것을 볼 수 있습니다.

![image](https://user-images.githubusercontent.com/68156400/124756093-114fcc80-df67-11eb-9b09-4171dd2950c1.png)

8.왜냐면 build가 된 것은 Dockerfile뿐이며, 나머지 server.js나 package.json 파일이 포함되지가 않았기 때문입니다.

따라서 npm install을 베이스라인인 node:10에서 실행하지만, npm install을 하기 위해선 package.json이 필요하므로, 오류가 발생하게 됩니다.

![image](https://user-images.githubusercontent.com/68156400/124756124-190f7100-df67-11eb-907c-8f1d35e4ed95.png)

8.오류를 제거하기 위해 package.json 파일을 빌드할 때 복사하도록 설정합니다.

(코드에선 package-lock.json에 복사를 위해 package\*.json을 입력했지만 docker를 공부할 때 중요한 건 아니니 넘어갑니다.)

![image](https://user-images.githubusercontent.com/68156400/124756184-24fb3300-df67-11eb-8198-577e578c1aab.png)


9.이제 다시 빌드를 위해 docker build 명령어를 사용합니다.

Dockerfile에 입력한 순서대로 node에서 package.json 파일을 복사한 후,

복사한 package.json 파일을 읽어 필요한 종속성들을 npm install로 다운 받습니다.

![image](https://user-images.githubusercontent.com/68156400/124756272-3cd2b700-df67-11eb-82e4-e1c191bec22d.png)

10.이제 방금 build한 이미지로 컨테이너를 생성하면,

지금까지 안나온 CMD 즉, 컨테이너를 실행할 때 실행되는 명령어들이 실행될 것입니다.

허나, 막상 실행해보면 원하는 오류가 뜹니다.

![image](https://user-images.githubusercontent.com/68156400/124756285-40fed480-df67-11eb-9587-088353494e79.png)

11.왜나하면 현재 이미지를 만드는데 사용 된 것을은 node10과 package.json파일입니다.

막상 서버를 실행하는데 필요한 server.js가 추가 되지 않았습니다.

따라서 Dockerfile에 COPY예 server.js를 추가해서 다시 이미지를 빌드합니다.

![image](https://user-images.githubusercontent.com/68156400/124756305-4825e280-df67-11eb-93ce-c192939e8c34.png)
![image](https://user-images.githubusercontent.com/68156400/124756330-4e1bc380-df67-11eb-9853-311d44ca2fdf.png)


12.다시 만들어진 이미지를 실행하여, 컨테이너화 시켜줍니다.

밑에서 보기와 같이 실행이 되는 것을 볼 수 있습니다.

![image](https://user-images.githubusercontent.com/68156400/124756349-52e07780-df67-11eb-815b-1b1e5965b961.png)

13.웹브라우저로 해당하는 포트로 접속하면 컨테이너화한 어플리케이션에 접속이 되지 않은 것을 볼 수 있습니다.

왜냐? run할 때 포트 맵핑을 안해줬기 때문입니다.

![image](https://user-images.githubusercontent.com/68156400/124756371-596eef00-df67-11eb-8070-d15f43553d00.png)

14.이를 수정하기 위해, 'docker run -p 로컬호스트:네트워크 이미지이름'을 다시 실행해줍니다.

![image](https://user-images.githubusercontent.com/68156400/124756426-67247480-df67-11eb-9c69-23d5a3502a1d.png)

15.로컬호스트(http://localhost:49160/)로 접속을 하면, server.js에서 /로 요청이 들어왔을 때, 반환하기로 한 문자열이 출력이 되는 것을 볼 수 있습니다.

![image](https://user-images.githubusercontent.com/68156400/124756436-6ab7fb80-df67-11eb-82dd-f53605bb5ac4.png)

16.working directory를 설정해줍니다.

working directory를 설정해주는 이유는

현재 실행 중인 컨테이너에 들어가 내부 디렉터리 구조를 보면 아래와 같이 되어 있는 것을 볼 수 있습니다.

node와 실행 중인 어플리케이션이 컨테이너에 최상위층에 모두 저장 되어있는 것을 볼 수 있습니다.

![image](https://user-images.githubusercontent.com/68156400/124756452-6f7caf80-df67-11eb-9c8a-d46cf232dbc7.png)

이와 같은 현상을 방지하기 위해 워킹 디렉터리를 따로 설정하여, 디렉터리 구조를 분리할 수 있습니다.

17.Dockerfile에 워킹디렉토리 설정하기.

![image](https://user-images.githubusercontent.com/68156400/124756471-73a8cd00-df67-11eb-80f2-6fa7c3d8a217.png)

18\. 다시 빌드해서 

![image](https://user-images.githubusercontent.com/68156400/124756493-786d8100-df67-11eb-9e76-a7195e25c188.png)

실행 한 후에, 

![image](https://user-images.githubusercontent.com/68156400/124756511-7c999e80-df67-11eb-9110-3f5c0b1887ce.png)

exec(실행 중인 컨테이너에서 명령어 실행하기)로 sh를 사용하여, 컨테이너 내부에서 쉘 실행합니다.

현재 위치를 pwd로 보면 워킹디렉터리로 설정 되어 있고, ls로 내부 목록을 보면, 프로젝트의 구성요소가 모두 들어가있는 것을 볼 수 있습니다.

![image](https://user-images.githubusercontent.com/68156400/124756522-802d2580-df67-11eb-8d2f-3f54fff65f6a.png)

Docker Volume에 대해

Docker Volume은 현재 프로젝트를 빌드할 때 일일이 파일들을 COPY해서 이미지를 빌드했지만, 

Docker Volume을 사용하면 로컬 저장소, 즉 현재 실행하고 있는 컴퓨터에 하드디스크에 있는 소스를 참조하여 컨테이너를 실행하는 것입니다.

밑에 이미지에서 위에 COPY가 우리가 지금까지 진행한 방법이고

볼륨이 아래와 같이 로컬에 있는 소스를 참조하는 방법입니다.

![image](https://user-images.githubusercontent.com/68156400/124756580-90450500-df67-11eb-969e-029eb4468778.png)

docker의 volume은 run에서 -v 플래그를 사용합니다.

docker run -p 5000:8080 -v /usr/src/app/node\_modules -v $(pwd):/usr/src/app username/projectname:version3 

첫번째 -v의 인자인 /usr/src/app/node\_modules는 로컬에 저장되어 있지 않으므로 참고할 대상이 없습니다. 그러므로 해당 요소는 참고하지 말라는 뜻입니다.

두번째 -v의 인자인 $(pwd):/usr/src/app가 있는데,

여기서 $(pwd)는 현재 로컬의 소스를 가리키며, /usr/src/app이 $(pwd)를 참조하라는 뜻으로 사용됩니다.

마지막의 username/projectname:version3은 컨테이너화할 이미지를 뜻합니다.

![image](https://user-images.githubusercontent.com/68156400/124756606-976c1300-df67-11eb-923c-e763c48e41e7.png)

이제 다시 접속을 해보면. 밑에와 같이 접속이 됩니다.

![image](https://user-images.githubusercontent.com/68156400/124756618-9dfa8a80-df67-11eb-83ba-b17eb4ac8744.png)

이 상태에서 server.js에 들어가서 res.send에 문자열을 바꿔줍니다.

현재 상황에선 localhost:5000으로 다시 들어가도 문자열이 바뀌지 않습니다.

![image](https://user-images.githubusercontent.com/68156400/124756628-a226a800-df67-11eb-8e19-4de6d1cd517b.png)

그러나 컨테이너를 다시 실행하면

아래와 같이 이미지를 다시 빌드하지 않아도 코드의 내용이 수정되는 것을 알 수 있습니다.

이젠, 매 번 이미지를 빌드하지 않고, volume 옵션을 준 후, 컨테이너를 재실행하면 됩니다!

![image](https://user-images.githubusercontent.com/68156400/124756646-a783f280-df67-11eb-9851-12804b32f5ce.png)
