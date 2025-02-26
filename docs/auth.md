# Fluxo de autenticação & autorização

## Como funciona? 
Utilizamos o protocolo OAuth2 [^1] com o Google como Authorization Server.

     +--------+                               +---------------+
     |        |--(A)- Authorization Request ->|   Resource    |
     |        |                               |     Owner     |
     |        |<-(B)-- Authorization Grant ---|               |
     |        |                               +---------------+
     |        |
     |        |                               +---------------+
     |        |--(C)-- Authorization Grant -->| Authorization |
     | Client |                               |     Server    |
     |        |<-(D)----- Access Token -------|               |
     |        |                               +---------------+
     |        |
     |        |                               +---------------+
     |        |--(E)----- Access Token ------>|    Resource   |
     |        |                               |     Server    |
     |        |<-(F)--- Protected Resource ---|               |
     +--------+                               +---------------+


Foi implementada a arquitetura da seção 6.2 da [^2]


                       +---------------+      +----------+  +----------+
                       |               |      |          |  |          |
                       | Authorization |      |  Token   |  | Resource |
                       |   Endpoint    |      | Endpoint |  |  Server  |
                       |               |      |          |  |          |
                       +---------------+      +----------+  +----------+

                                ^                   ^              ^
                                |                (F)|              |
                                |                   v              |
                                |                                  |
                                |   +---------------------------+  |
                                |   |                           |  |
                                |   |  Token-Mediating Backend  |  |(J)
                             (D)|   |                           |  |
                                |   +---------------------------+  |
                                |                                  |
                                |       ^     ^     ^     +        |
                                |  (B,I)|  (C)|  (E)|  (G)|        |
                                v       v     v     +     v        v

 +-----------------+         +-----------------------------------------+
 |                 |  (A,H)  |                                         |
 | Static Web Host | +-----> |                 Browser                 |
 |                 |         |                                         |
 +-----------------+         +-----------------------------------------+


O backend é simultaneamente o client e o resource server da arquitetura OAuth2, dessa forma, caso algum cliente da API queria consumir nosso dados, o login desse ser feito em: `$BACKEND/api/oauth2/authorization/google`,
Quando bem sucedido, o usuário retornará a página que fez o login (ex: frontend) com um cookie (JSESSIONID), ao enviar esse cookie para o backend, iremos adquiri o token JWT do usuário no lado do servidor e utiliza-lo para
autenticação e autorização, dessa forma o token do google ou de outro provedor não fica exposto no navegador evitando problemas de segurança.

Outra vantagem é que nenhum outro serviço precisa se preocupar em ser um client na arquitetura OAuth2, visto que tudo já está centralizado no backend


### Referências

[^1]: https://datatracker.ietf.org/doc/html/rfc6749[The OAuth 2.0 Authorization Framework]
[^2]: https://datatracker.ietf.org/doc/html/draft-ietf-oauth-browser-based-apps-18[OAuth 2.0 for Browser-Based Applications]
[^3]: [[[spring-issue]]] https://github.com/spring-projects/spring-security/issues/7808#issuecomment-704548457[Spring Security Issue #7808]
[^4]: [[[google-openid]]] https://developers.google.com/identity/openid-connect/openid-connect[Google Identity: OpenID Connect]
[^5]: [[[openid-connect]]] https://openid.net/specs/openid-connect-core-1_0.html#IDToken[OpenID Connect Core 1.0 - ID Token]
[^6]: [[[spring-security-oauth2]]] https://docs.spring.io/spring-security/reference/servlet/oauth2/index.html[Spring Security OAuth2]
