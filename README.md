# OAtuth2.0
OAtuth에 대한 이해

## OAtuth란?
> OAuth 2.0(Open Authorization 2.0, OAuth2)은 인증을 위한 개방형 표준 프로토콜입니다. 
> 이 프로토콜에서는 Third-Party 프로그램에게 리소스 소유자를 대신하여 리소스 서버에서 제공하는 자원에 대한 접근 권한을 위임하는 방식을 제공합니다.
> 
> 구글, 페이스북, 카카오, 네이버 등에서 제공하는 간편 로그인 기능도 OAuth2 프로토콜 기반의 사용자 인증 기능을 제공하고 있으며, 한컴인텔리전스의 IoT 플랫폼인 NeoIDM(네오아이디엠) 또한 > OAuth2.0 인증 프레임워크를 기반으로 하여 사용자 인증을 수행합니다.

## OAuth 2.0 주요 용어

| 용어 |내용|
|-----|------------------|
|Authentication|인증, 접근 자격이 있는지 검증하는 단계를 말합니다.|
|Authorization|인가, 자원에 접근할 권한을 부여하는 것입니다. 인가가 완료되면 리소스 접근 권한이 담긴 Access Token이 클라이언트에게 부여됩니다.|
|Access Token|리소스 서버에게서 리소스 소유자의 보호된 자원을 획득할 때 사용되는 만료 기간이 있는 Token입니다.|
|Refresh Token|Access Token 만료시 이를 갱신하기 위한 용도로 사용하는 Token입니다. Refresh Token은 일반적으로 Access Token보다 만료 기간이 깁니다.|

## Roles - OAuth 2.0의 4가지 역할

OAuth2 프로토콜에서는 다양한 클라이언트 환경에 적합하도록 권한 부여 방식에 따른 프로토콜을 4가지 종류로 구분하여 제공하고 있습니다.

### 1. Authorization Code Grant 권한 부여 승인 코드 방식
> 권한 부여 승인을 위해 자체 생성한 Authorization Code를 전달하는 방식으로 많이 쓰이고 기본이 되는 방식입니다. 간편 로그인 기능에서 사용되는 방식으로 클라이언트가 사용자를 대신하여  > 특정 자원에 접근을 요청할 때 사용되는 방식입니다. 보통 타사의 클라이언트에게 보호된 자원을 제공하기 위한 인증에 사용됩니다. Refresh Token의 사용이 가능한 방식입니다.

![Authorization-Code](https://user-images.githubusercontent.com/71062404/206860591-b6e29278-e83d-4cc4-896f-483e52548830.png)

> 권한 부여 승인 요청 시 response_type을 code로 지정하여 요청합니다. 이후 클라이언트는 권한 서버에서 제공하는 로그인 페이지를 브라우저를 띄워 출력합니다. 이 페이지를 통해 사용자가 로 > 그인을 하면 권한 서버는 권한 부여 승인 코드 요청 시 전달받은 redirect_url로 Authorization Code를 전달합니다. Authorization Code는 권한 서버에서 제공하는 API를 통해 Access Token > 으로 교환됩니다. 

### 2. Implicit Grant 암묵적 승인 방식
> 자격증명을 안전하게 저장하기 힘든 클라이언트(ex: JavaScript등의 스크립트 언어를 사용한 브라우저)에게 최적화된 방식입니다. 
> 암시적 승인 방식에서는 권한 부여 승인 코드 없이 바로 Access Token이 발급 됩니다. Access Token이 바로 전달되므로 만료기간을 짧게 설정하여 누출의 위험을 줄일 필요가 있습니다. 
> Refresh Token 사용이 불가능한 방식이며, 이 방식에서 권한 서버는 client_secret를 사용해 클라이언트를 인증하지 않습니다. Access Token을 획득하기 위한 절차가 간소화되기에 응답성과 > 효율성은 높아지지만 Access Token이 URL로 전달된다는 단점이 있습니다.

![Implicit](https://user-images.githubusercontent.com/71062404/206860594-3fbcb765-0175-49dd-9652-5bba29e9e7c1.png)

> 권한 부여 승인 요청 시 response_type을 token으로 설정하여 요청합니다. 이후 클라이언트는 권한 서버에서 제공하는 로그인 페이지를 브라우저를 띄워 출력하게 되며 로그인이 완료되면 권 > 한 서버는 Authorization Code가 아닌 Access Token를 redirect_url로 바로 전달합니다. 

### 3. Resource Owner Password Credentials Grant 자원 소유자 자격증명 승인 방식
> 간단하게 username, password로 Access Token을 받는 방식입니다.
> 클라이언트가 타사의 외부 프로그램일 경우에는 이 방식을 적용하면 안됩니다. 자신의 서비스에서 제공하는 어플리케이션일 경우에만 사용되는 인증 방식입니다. Refresh Token의 사용도 가능> 합니다.

![password](https://user-images.githubusercontent.com/71062404/206860595-3bf27d60-8688-4a47-8282-17365a016073.png)

> 위와 같이 흐름은 간단합니다. 제공하는 API를 통해 username, password을 전달하여 Access Token을 받는 것입니다. 중요한 점은 이 방식은 권한 서버, 리소스 서버, 클라이언트가 모두 같> 은 시스템에 속해 있을 때 사용되어야 하는 방식이라는 점입니다.

### 4. Client Credentials Grant 클라이언트 자격증명 승인 방식
> 클라이언트의 자격증명만으로 Access Token을 획득하는 방식입니다.
> OAuth2의 권한 부여 방식 중 가장 간단한 방식으로 클라이언트 자신이 관리하는 리소스 혹은 권한 서버에 해당 클라이언트를 위한 제한된 리소스 접근 권한이 설정되어 있는 경우 사용됩니다.
> 이 방식은 자격증명을 안전하게 보관할 수 있는 클라이언트에서만 사용되어야 하며, Refresh Token은 사용할 수 없습니다.

![Client-Credentials](https://user-images.githubusercontent.com/71062404/206860593-86865a2e-a09f-467e-9a48-8872118e1cdf.png)

## Request and Response Examples

+ client_id, client_secret
  + 클라이언트 자격증명. 클라이언트가 권한 서버에 등록하면 발급받을 수 있으며 권한 서버 연동 시 클라이언트의 검증에 사용됩니다.

+ redirect_url
  + 권한 서버가 요청에 대한 응답을 보낼 url을 설정합니다.

+ response_type
  + 권한 부여 동의 요청 시 포함되는 값으로 권한 부여 방식에 대한 설정입니다.
  + 아래 값 중 한 개를 사용합니다.
     + code: Authorization Code Grant
     + token: Implicit Grant

+ state
   + CSRF 공격에 대비하기 위해 클라이언트가 권한서버에 요청 시 포함하는 임의의 문자열. 필수 사항은 아니지만 클라이언트가 요청 시 state를 포함 시켰다면 권한 서버는 동일한 값을 클라이언트에게 보내야 합니다.

+ grant_type
   + Access Token 획득 요청 시 포함되는 값으로 권한 부여 방식에 대한 설정입니다. 아래 값 중 한 개를 사용합니다.
     + authorization_code: Authorization Code Grant
     + password: Resource Owner Password Credentials Grant
     + client_credentials: Client Credentials Grant

+ code
  + Authorization Code Grant 방식에서 Access Token요청 시 사용됩니다. 권한 서버에서 획득한 Authorization Code를 입력합니다.

+ token_type
  +발행된 Token의 타입. 대표적으로 Bearer, MAC(Message Authentication Code)가 있습니다.

+ expires_in
  + 토큰 만료 시간(단위: 초)

+ example_parameter
  + Token 타입에 따른 추가 파라미터
  
### 1.  Authorization Code Grant 권한 부여 승인 코드 방식


#### Step 1: Authorization

+ Request
 + (GET)/authorize?response_type=code&client_id=s6BhdRkqt3&state=xyz&redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fc

+ Response
  + https://client.example.com/cb?code=SplxlOBeZQQYbYS6WxSbIA&state=xyz

#### Step 2: Access Token

+ Request
  + (POST) /token
  + Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
  + Content-Type: application/x-www-form-urlencoded
  + grant_type=authorization_code&code=SplxlOBeZQQYbYS6WxSbIA&redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb

+ Response
  + {

  +          "access_token":"2YotnFZFEjr1zCsicMWpAA",

  +          "token_type":"example",

  +          "expires_in":3600,

  +          "refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA",

  +          "example_parameter":"example_value"}

+ 특이사항
  +Authorization Code 획득 후 해당 Code로 Access Token 획득
  
### 2.  Implicit Grant 암묵적 승인 방식

+ Request
  + (GET)/authorize?response_type=token&client_id=s6BhdRkqt3&state=xyz&redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb

+ Response
  + http://example.com/cb#access_token=2YotnFZFEjr1zCsicMWpAA&state=xyz&token_type=example&expires_in=3600

+ 특이사항
   +Authorize 요청 시 url로 Access Token이 바로 전달됨
 
### 3.  Resource Owner Password Credentials Grant 자원 소유자 자격증명 승인 방식
+ Request
  + (POST) /token
  + Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
  + Content-Type: application/x-www-form-urlencoded
  + grant_type=password&username=johndoe&password=A3ddj3w

+ Response
  + {

  +          "access_token":"2YotnFZFEjr1zCsicMWpAA",
  
  +          "token_type":"example",

  +          "expires_in":3600,

  +          "refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA",

  +          "example_parameter":"example_value"}

+ 특이사항
  + Username, Password로 Access Token 획득
  
### 4.  Client Credentials Grant 클라이언트  자격증명 승인 방식

+ Request
  + (POST) /token
  + Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
  + Content-Type: application/x-www-form-urlencoded
  + grant_type=client_credentials
  
+ Response
  + {

  +          "access_token":"2YotnFZFEjr1zCsicMWpAA",

  +          "token_type":"example",

  +          "expires_in":3600,

  +          "example_parameter":"example_value"}

+ 특이사항
  + 클라이언트 자격증명만으로 Access Token 획득
  
  
[출처] OAuth 2.0 동작 방식의 이해|작성자 MDS인텔리전스
https://blog.naver.com/mds_datasecurity/222182943542
