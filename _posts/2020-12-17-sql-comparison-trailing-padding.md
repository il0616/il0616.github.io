---
title: SQL WHERE 절 비교 연산 시 후행공백 있는 경우
date: 2020-12-17 23:03:00 +0900
categories:
  - SQL
links:
  - name: ANSI/ISO SQL-92
    url: http://www.contrib.andrew.cmu.edu/~shadow/sql/sql1992.txt
  - name: SQL Server가 문자열을 후행 공백과 비교 하는 방법
    url: http://support.microsoft.com/kb/316626
  - name: MySQL에서 'a' = 'a '가 true로 평가된다?
    url: https://woowabros.github.io/study/2018/02/26/mysql-char-comparison.html
---

```sql
SELECT * FROM student WHERE name = 'redone'
SELECT * FROM student WHERE name = 'redone '
```

첫번째 SQL문은 name 조건값이 `redone`이고
두번째 SQL문의 name 조건값이 `redone `으로 뒤에 공백이 하나 추가되어 있다.

하지만, 두 SQL의 결과는 많은 RDBMS(`MySQL(MariaDB)`, `Oracle`, `MS SQL`)에서는 동일하다.
해당 RDBMS에서는 `ANSI/ISO SQL-92` 사양에 맞게 구현했기 때문이다. 아래는 발췌한 부분이다.

```
a) If the length in characters of X is not equal to the length
  in characters of Y, then the shorter string is effectively
  replaced, for the purposes of comparison, with a copy of
  itself that has been extended to the length of the longer
  string by concatenation on the right of one or more pad char-
  acters, where the pad character is chosen based on CS. If
  CS has the NO PAD attribute, then the pad character is an
  implementation-dependent character different from any char-
  acter in the character set of X and Y that collates less
  than any string under CS. Otherwise, the pad character is a
  <space>.
```

비교할 string의 길이가 맞지 않는 경우, 동일길이로 맞추기 위해 짧은 string의 뒷부분에 패딩처리하였기 때문에 맨 위의 2가지 SQL의 결과는 동일하게 나온다.  
제목에서는 WHERE 절에 한정해서 적어두었지만, WHERE절에서 뿐만 아니라 모든 비교연산에서 동일하게 패딩 처리된다.

= 뿐만 아니라 다른 비교연산자인 >, >=, <, => 등도 동일하다. 다만, `LIKE`의 경우는 비교연산자가 아니라서 패딩처리를 하지 않아 다른 결과가 나타난다.
