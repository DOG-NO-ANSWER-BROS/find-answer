# HTTP ์ผํธ~~ ๐

HTTP ๋ Hypertext Transfer Protocol์ ์ฝ์๋ก, ์๋ฒ-ํด๋ผ์ด์ธํธ ๊ฐ์ ํต์ ์ request-response ํ์์ผ๋ก ๋ฐ๊ฒ ๋ค๋ ๊ท์น์ ์ ํ ํ๋กํ ์ฝ์๋๋ค.

HTTPS๋ ์ํธํ๋ HTTP ์ฐ๊ฒฐ์ ๋งํด์. ์์ ์๋ SSL(Secure Sockets Layer)์ ํตํด์ ์ํธํํ๋๋ฐ,
์์ฆ์๋ TLS(Transport Layer Security)๋ฅผ ํตํด ํ์ฃ . ์ด ๊ฒฝ์ฐ http:// ๊ฐ ์๋ https:// ๋ฅผ ์ฌ์ฉํด์ผํ๋ต๋๋ค.
HTTPS๋ ํํ๋ค 'HTTP over TLS' ๋ผ๊ณ  ํํํด์. (์ฆ, TLS๋ HTTPS ์ธ์ ๋ค๋ฅธ ๊ณณ์์๋ ์ฐ์๋๋ค.)

## HTTP ์ธ์
HTTP ํด๋ผ์ด์ธํธ๊ฐ TCP(Transmission Control Protocol) ์ปค๋ฅ์์ ์๋ฒ์ ํฌํธ์ ์ฐ๊ฒฐํ๋ฉฐ HTTP ์ธ์์ ์์๋ฉ๋๋ค.
๋ณดํต ๊ทธ ํฌํธ๋ 80๋ฒ์ด์ง๋ง, 8080๋ฒ์ด ์ฐ์ผ ๋๋ ์์ฃ . ์๋ฒ๋ ๊ทธ ํฌํธ์์ ๊ธฐ๋ค๋ฆฌ๊ณ  ์๋ค๊ฐ ํด๋ผ์ด์ธํธ๋ก๋ถํฐ ๋ฆฌํ์คํธ(request)๊ฐ ๋ค์ด์ค๋ฉด
์์ ๋ง์ ๋ฉ์์ง๋ฅผ ๋ณด๋๋๋ค. ์ด๊ฒ์ด ๋ฐ๋ก ๋ฆฌ์คํฐ์ค(response)์ฃ .

์ด request-response ํธ๋์ญ์์ ํ๋์ HTTP ์ธ์์ด๋ผ๊ณ  ํฉ๋๋ค.

## ๋ฉ์์ง ํํ

๋ฆฌํ์คํธ์ ๋ฆฌ์คํฐ์ค ๊ฐ๊ฐ์ ๋ฉ์์ง ํํ๋ฅผ ์ดํด๋ณด๋ฉด ์ข๊ฒ ๋ค์.

### ๋ฆฌํ์คํธ
* ๋ฆฌํ์คํธ ์ค (ex: GET /images/logo.png HTTP 1.1)
* [๋ฆฌํ์คํธ ํค๋ ํ๋](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Request_fields)
* ๋น์ด์๋ ์ค
* [์ ํ์  ๋ฉ์์ง body](https://en.wikipedia.org/wiki/HTTP_message_body)

### ๋ฆฌ์คํฐ์ค
* status ์ค, status๊ฐ ๋ ์ด์ ์ ํจ๊ป (ex: HTTP/1.1 200 OK)
* ๋ฆฌ์คํฐ์ค ํค๋ ํ๋ (ex: Content-Type: text/html)
* ๋น์ด์๋ ์ค
* [์ ํ์  ๋ฉ์์ง body](https://en.wikipedia.org/wiki/HTTP_message_body)

### ๋ฌผ๋ก 
๋ฆฌํ์คํธ ๋ฉ์๋(GET, POST, DELETE... ๋ฑ)๋ ๋ฆฌ์คํฐ์ค status์ ๊ด๋ จํด์ ๊ณต๋ถํด๋ณด๋ฉด ์ข๊ฒ ์ง๋ง,
์ถฉ๋ถํ ๊ทธ๊ฒ๋ง์ ์ฃผ์ ๋ก ๋ค๋ค๋ ์ด์ํ์ง ์์ ๋๋ค์ด๋ผ ์ง๊ธ์ ๋ค๋ฃจ์ง ์์ต๋๋ค.