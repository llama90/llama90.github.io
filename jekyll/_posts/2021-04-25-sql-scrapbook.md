---
title: SQL Scrapbook
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
tags:
- SQL
- Scrapbook
toc: true
toc_sticky: true
markdown: kramdown
highlighter: rouge
categories:
- Scrapbook
---

# [DELETE에서의 서브쿼리 활용](https://velog.io/@khnn/TIL-DELETE%EC%97%90%EC%84%9C%EC%9D%98-%EC%84%9C%EB%B8%8C%EC%BF%BC%EB%A6%AC-%ED%99%9C%EC%9A%A9)
DELETE에서 서브쿼리 결과를 재활용 하는 경우, SELECT 문과 같이 동작하지 않는것 같아 찾아보았다.

[해당](https://shinheechul.tistory.com/30) 글도 찾아봤는데, 데이터베이스마다 동작이 다른것 같다. 결과적으로 서브쿼리를 추가하는 방식으로 해결했다.

테이블 A와 테이블 B가 존재할때, 두 테이블의 조인 결과를 바탕으로 테이블 A의 데이터를 삭제하는 경우
```sql
DELETE from ${TABLE A}
WHERE id IN (
	SELECT ${ALIAS}.id
	FROM (
		SELECT ${TABLE A}.id
		FROM ${TABLE B}
			LEFT OUTER JOIN ${TABLE A}
			ON ${JOIN CONDITIONS}
	) ${ALIAS}
);	
```

# MSSQL - [SQL Convert Date functions and formats](https://www.sqlshack.com/sql-convert-date-functions-and-formats/)
# Mysql - [Date and Time Functions](https://dev.mysql.com/doc/refman/8.0/en/date-and-time-functions.html)
# Postgres - [Data Type Formatting Functions](https://www.postgresql.org/docs/current/functions-formatting.html)
# Oracle - [Date Format Types](https://docs.oracle.com/cd/E41183_01/DR/Date_Format_Types.html)
# Regex - [Validate Traditional Date Formats](https://www.oreilly.com/library/view/regular-expressions-cookbook/9781449327453/ch04s04.html)



