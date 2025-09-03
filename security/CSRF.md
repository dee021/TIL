# CSRF(Cross-Site Request Forgery; 크로스 사이트 간 요청 위조)
권한이 있는 사용자가 자신도 모르게 요청을 전송하게 하는 공격 방식이다.

## 예시
A 사이트에 다음과 같이 특정 사용자의 등급을 변경하는 URI가 존재하는 것을 공격자가 알았고, 필요한 파라미터를 안다고 가정

> www.aaa.xxx/update?grade=admin&account=123

공격자는 A 사이트의 관리자(피해자)가 자주 방문하는 B 사이트에 다음과 같이 html 태그를 이용해서 게시물을 작성

```html
<form action='www.aaa.xxx/update?grade=admin&account=123'>
    <input type='submit' value='축 이벤트 당첨'>
</form>

<img src='www.aaa.xxx/update?grade=admin&account=123'>
```

A 사이트의 관리자(피해자)가 해당 게시물을 조회하여 html 태그에 사용된 URI을 호출하면 A 서버에는 로그인한 관리자의 요청에 의하여 공격자는 admin 등급의 사용자로 변경된다.


## 방어 방법
1. CSRF 토큰
사용자가 사이트를 이용할 때 매번 변경되는 문자열을 생성하고 이를 요청 시에 검증하는 방식으로, 문자열이 변경되기 때문에 해당 문자열을 알지 못하면 공격 대상 사이트는 요청을 처리하지 않도록 한다.
- 일반적으로 세션이 생성될 때 CSRF 토큰을 같이 발생하기 때문에 CSRF 토큰은 매번 변경되는데 스프링 시큐리티의 경우 GET 방식을 제외한 모든 요청에 CSRF 토큰을 전송하게 되어 있다.

2. SameSite 쿠키 속성
쿠키의 SameSite 속성을 설정하여 다른 사이트에서 해당 사이트에 접근하는 것을 제한하는 방법이다.

3. HTTP 헤더 검증
요청 헤더에 포함된 정보 (ex. Referer)를 검증하여 요청 출처를 확인하는 방법이다.
