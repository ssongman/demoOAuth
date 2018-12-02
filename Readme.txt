

<< 설명 >>
ㅇ 본 소스는 OAuth 서버를 구성하는 코드이다.

ㅇ 구성요소
1) RS (Resource Server)
2) AS (Authorization Server)
3) CA (Client Application)
4) RO (Resource Owner, User)



-- curl 로 테스트 해보다가 인증받는 부분은 실패함... postman에서는 잘 되는데 왜 안될까...
<< TEST >>    

1. 인증
post 명령으로 받아야 함.

1) 요청시도
set CLIENTID=keesun-client
set CLIENTSECRET=keesun-client
curl -v -X POST --basic -u %CLIENTID%:%CLIENTSECRET% ^
   -H "Content-Type: application/x-www-form-urlencoded;charset=UTF-8" ^
   -k -d "username=keesun&password=pass&grant_type=password" ^
   http://localhost:8080/oauth/token
ㅇ 결과 : 실패
{"timestamp":"2018-12-02T15:12:40.943+0000","status":401,"error":"Unauthorized","message":"Unauthorized","path":"/oauth/token"}* Connection #0 to host localhost left intact


2) 요청시도
set CLIENTID=keesun-client
set CLIENTSECRET=keesun-client
curl -v -X POST --basic -u %CLIENTID%:%CLIENTSECRET% ^
   -H "Content-Type: application/x-www-form-urlencoded;charset=UTF-8" ^
   -k -d "username=keesun&password=pass&grant_type=password" ^
   http://localhost:8080/oauth/token
ㅇ 결과 : 실패
{"timestamp":"2018-12-02T15:16:12.925+0000","status":401,"error":"Unauthorized","message":"Unauthorized","path":"/oauth/token"}* Connection #0 to host localhost left intact



3) postman 의 코드값
POST /oauth/token HTTP/1.1
Host: localhost:8080
Content-Type: application/x-www-form-urlencoded
Authorization: Basic a2Vlc3VuLWNsaWVudDprZWVzdW4tcGFzcw==
Cache-Control: no-cache
Postman-Token: aed7a090-d746-b956-b596-301b45243eee

username=keesun&password=pass&grant_type=password


ㅇ 결과
{
    "access_token": "daa600dd-c567-4a9d-b8cf-9310268aa3bd",
    "token_type": "bearer",
    "refresh_token": "58e979f7-ed3b-4d25-bb26-1410f8765b31",
    "expires_in": 3599,
    "scope": "read write trust"
}



2.  GET - 파라메타 방식
1) postman code 값
GET /users/user?access_token=5ce1183c-6543-4265-86d9-8959fc3b480d HTTP/1.1
Host: localhost:8080
Cache-Control: no-cache
Postman-Token: c9ae16f6-52e2-a21d-93bb-981f81da7610

http://localhost:8080/users/user?access_token=5ce1183c-6543-4265-86d9-8959fc3b480d

ㅇ 결과
[
    {
        "id": 1,
        "username": "keesun",
        "password": "$2a$10$TO6hTIVGuOXdVKcyuCglnu0L7M/f.N3sOrJzdzSyCDCeBRIKeOYyi"
    }
]


2) curl 실행문
curl http://localhost:8080/users/user?access_token=daa600dd-c567-4a9d-b8cf-9310268aa3bd
ㅇ 결과 : 성공
[{"id":1,"username":"keesun","password":"$2a$10$TO6hTIVGuOXdVKcyuCglnu0L7M/f.N3sOrJzdzSyCDCeBRIKeOYyi"}]



3.  GET - headers의 bearer 방식
1) postman code
GET /users/user HTTP/1.1
Host: localhost:8080
Authorization: bearer daa600dd-c567-4a9d-b8cf-9310268aa3bd
Cache-Control: no-cache
Postman-Token: c2bfd266-1fe1-435c-942a-27a205c2fced
ㅇ 결과 
[
    {
        "id": 1,
        "username": "keesun",
        "password": "$2a$10$TO6hTIVGuOXdVKcyuCglnu0L7M/f.N3sOrJzdzSyCDCeBRIKeOYyi"
    }
]

2) curl 시도
curl -H "Authorization: bearer daa600dd-c567-4a9d-b8cf-9310268aa3bd" http://localhost:8080/users/user

ㅇ 결과 : 성공
[{"id":1,"username":"keesun","password":"$2a$10$TO6hTIVGuOXdVKcyuCglnu0L7M/f.N3sOrJzdzSyCDCeBRIKeOYyi"}]




4. Refresh token
1) postman code
POST /oauth/token HTTP/1.1
Host: localhost:8080
Content-Type: application/x-www-form-urlencoded
Authorization: Basic a2Vlc3VuLWNsaWVudDprZWVzdW4tcGFzcw==
Cache-Control: no-cache
Postman-Token: fd9ba65b-3d63-f9e6-de67-d2ca0da7daa2

grant_type=refresh_token&refresh_token=58e979f7-ed3b-4d25-bb26-1410f8765b31

ㅇ 결과
{
    "access_token": "ccd9b4dd-a137-4116-9647-62243f628b5f",
    "token_type": "bearer",
    "refresh_token": "58e979f7-ed3b-4d25-bb26-1410f8765b31",
    "expires_in": 3599,
    "scope": "read write trust"
}

