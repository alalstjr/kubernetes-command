# 명령어

## Document

https://kubernetes.io/  
https://kubernetes.io/ko/docs/home/

## 관리

> kubectl get pod -n kube-system  
> kubectl get pod --namespace kube-system

쿠버네티스 주요 컴포넌트 확인하기  
쿠버네티스가 동작하는데 필요한 컴포넌트를 확인하는 명령어  

> kubectl describe pod {pod 이름}

쿠버네티스 pod 진행상황, 진행 로그 상세보기 네임스페이스로 검색한다.

> kubectl get all -n [namespace]

## 객체의 설정 업데이트

> kubectl edit

edit 명령은 리소스의 설정 정보를 kubectl 이 설치되어 있는 머신의 에디터를 이용해서 에디트할 수 있다.  
예를 들어 hello-deployment 라는 이름의 deployment 리소스의 설정을 에디트 하고 싶으면,
`kubectl edit deploy hello-deployment`  
명령을 실행하면, (맥북 기준) vi 에디터가 실행되고, hello-deployment  설정 정보를 vi 에디터로 편집할 수 있게해준다.   
에디터에서 설정을 변경한 후 저장하면, 그 객체의 설정값이 업데이트 된다.  

> kubectl replace

설정 파일 수정하거나, 설정 파일을 새로 만들어서 그 파일로 설정을 업데이트하고 싶을때는  replace 명령을 사용한다.  
`%kubectl replace -f [YAML 파일명]`  
으로 이용한다.

> kubectl patch

patch 명령은 파일 업데이트 없이 기존 리소스의 설정 정보 중 여러개의 필드를 수정하고자 하는데 이용한다.  
%kubectl patch [리소스 종류] [리소스명] --patch ‘[YAML이나 JSON 포맷으로 변경하고자 하는 설정]’ 을 넣어주면 된다.  

예를 들어  
%kubectl patch deployment hello-deployment --patch 'spec:\n template:\n  spec:\n   containers:\n   - name: hello-deployment\n     image: gcr.io/terrycho-sandbox/deployment:v2’  
명령은 deployment 중에 hello-deployment 리소스에 대해서 image 명을  image: gcr.io/terrycho-sandbox/deployment:v2 로 변경한 예이다.  
특히 [YAML] 설정은 한줄에 써야 하는 만큼 띄어쓰기에 조심하는 것이 좋다.  

patch나 edit의 경우 쉽게 설정을 업데이트할 수 있는 장점은 있지만, 업데이트에 대한 히스토리를 추적하기 어려운 만큼 가급적이면, 새로운 파일을 생성하고, 파일을 replace를 통해서 적용하는 것이 파일 단위로 변경 내용을 추적할 수 있기 때문에 이 방법을 권장한다.

## 롤백

Deployment은 롤링 업데이트이외에, 롤백도 손쉽게 지원한다.  
먼저 배포된 버전을 체크해보려면 kubectl rollout history deployment/[Deployment 이름]을 실행하면 기존에 배포된 버전을 확인할 수 있다.   
디폴트로는 2개의 버전을 유지하게 되어 있다.

1버전으로 롤백을 하려면 kubectl rollout undo deployment [ deployment 명 ] --to-revision=[롤백할 버전명] 을 하면, 그 버전으로 롤백이 된다.  
명령을 실행하면 아래와 같이 이전 버전으로 롤백이 되는 것을 확인할 수 있다.

롤백등을 위해서 Deployment를  RS를 여러 버전을 유지하고 있다.  
유지하는 버전의 수는  Deployment에서 설정이 가능하다.

## YAML

### 세션 고정하기

~~~
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  sessionAffinity: ClientIP  # Session 고정을 할 수 있습니다.
  selector:
    app.kubernetes.io/name: MyApp
  ports:
    - protocol: TCP
      port: 80         
      targetPort: 9376 
~~~

sessionAffinity: ClientIP

### EndPoint

https://kubernetes.io/docs/concepts/services-networking/service/#endpoints

~~~
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
---
apiVersion: v1
kind: Endpoints
metadata:
  name: my-service
  labels:
    kubernetes.io/service-name: my-service
subsets:
    - addresses:
        - ip : 223.130.200.104
        - ip : 199.201.110.204
      ports:
        - port: 80
~~~

POD 없는 서비스를 하나 만들고 엔드포인트를 만듭니다.

## 리눅스 명령어

- dd : 라인 삭제
- u : 삭제 복귀