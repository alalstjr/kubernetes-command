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

쿠버네티스 pod 진행상황, 진행 로그 상세보기

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