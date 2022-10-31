# Rate limit using Dapr

### Prerequisites

#### Install Dapr CLI using Helm

* helm repo add dapr https://dapr.github.io/helm-charts/
* helm repo update
* Install dapr on your cluster:
helm upgrade --install dapr dapr/dapr \
--version=1.1.2 \
--namespace dapr-system \
--create-namespace \
--wait

#### Verify Dapr installation on your cluster

Once the installation is complete, verify that the dapr-operator, dapr-placement, dapr-sidecar-injector and dapr-sentry pods are running in the dapr-system namespace:

* kubectl get pods --namespace dapr-system

``` Dapr installation result
NAME                                     READY     STATUS    RESTARTS   AGE
dapr-dashboard-7bd6cbf5bf-xglsr          1/1       Running   0          40s
dapr-operator-7bd6cbf5bf-xglsr           1/1       Running   0          40s
dapr-placement-7f8f76778f-6vhl2          1/1       Running   0          40s
dapr-sidecar-injector-8555576b6f-29cqm   1/1       Running   0          40s
dapr-sentry-9435776c7f-8f7yd             1/1       Running   0          40s

```

#### Open Dapr dashboard on your cluster
 
 * dapr dashboard -k


## Getting started

#### Deoloyment service, config and component

First you need to go inside k8s folder and then apply all the yaml file:

* cd k8s
* kubectl apply -f .

## Test rate limit

This This part shows how to use kubectl `port-forward` to connect to running service in a Kubernetes cluster. 

* kubectl port-forward svc/ratelimit 3000:8080 -n poc

If you open `localhost:3000` you should be able to see the response from echo server and it would be something like below:

```
{"errorCode":"ERR_DIRECT_INVOKE","message":"failed getting app id either from the URL path or the header dapr-app-id"}
```

Now if you try to request more than 2 request per second you'll get an error with `429` status code and response message will be `You have reached maximum request limit`.

```diff
Request URL: http://localhost:3000/
Request Method: GET
+ Status Code: 429 Too Many Requests
Remote Address: [::1]:3000
Referrer Policy: strict-origin-when-cross-origin
Content-Length: 39
Content-Type: text/plain; charset=utf-8
Date: Fri, 28 Oct 2022 11:23:09 GMT
Server: fasthttp
Traceparent: 00-00000000000000000000000000000000-0000000000000000-00
+ X-Rate-Limit-Duration: 1
+ X-Rate-Limit-Limit: 2.00
+ X-Rate-Limit-Request-Forwarded-For
+ X-Rate-Limit-Request-Remote-Addr: 127.0.0.1:37150
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9,fa;q=0.8
Cache-Control: no-cache
Connection: keep-alive
Cookie: ai_user=CF6lN|2022-09-01T09:28:00.491Z; io=KAo2mgtmeo8lxpwKAAAD
Host: localhost:3000
Pragma: no-cache
sec-ch-ua: "Chromium";v="106", "Google Chrome";v="106", "Not;A=Brand";v="99"
sec-ch-ua-mobile: ?0
sec-ch-ua-platform: "Windows"
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: none
Sec-Fetch-User: ?1
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.0.0 Safari/537.36
```

