# 정리
# 문법 정리

Groups and ranges

```
Chracter	뜻
---
|	        또는
()	        그룹
[]	        문자셋, 괄호안의 어떤 문자든
[^]	        부정 문자셋, 괄호안의 어떤 문가 아닐때
(?:)	        찾지만 기억하지는 않음
```
```
Quantifiers
Chracter	  뜻
---
?	          없거나 있거나 (zero or one)
*	          없거나 있거나 많거나 (zero or more)
+	          하나 또는 많이 (one or more)
{n}	          n번 반복
{min,}	          최소
{min,max}	  최소, 그리고 최대
```
```
Boundary-type
Chracter	  뜻
---
\b	          단어 경계
\B	          단어 경계가 아님
^	          문장의 시작
$	          문장의 끝
```
```
Character classes
Chracter	  뜻
---
\	          특수 문자가 아닌 문자
.	          어떤 글자 (줄바꿈 문자 제외)
\d	          digit 숫자
\D	          digit 숫자 아님
\w	          word 문자
\W	          word 문자 아님
\s	          space 공백
\S	          space 공백 아님
```

연습할 수 있는 사이트! : regexr.com/5mhou

# 자바에서의 정규표현식 활용

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class RegexExample {
	public static void main(String[] args)  {
            Pattern pattern = Pattern.compile("^[a-zA-Z]*$"); //영문자만
            String val = "abcdef"; //대상문자열
	
            Matcher matcher = pattern.matcher(val);
            System.out.println(matcher.find());
	}
}
```

String의 다양한 메소드에서도 활용된다.

- boolean matches(String regex)

  인자로 주어진 정규식에 매칭되는 값이 있는지 확인합니다.

- String replaceAll(String regex, String replacement)

  문자열내에 있는 정규식 regex와 매치되는 모든 문자열을 replacement문자열로 바꾼 문자열을 반환합니다.

- String[] split(String regex)

  인자로 주어진 정규식과 매치되는 문자열을 구분자로 분할합니다.

# 질문
