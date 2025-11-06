```sql

/*****************************************************

  1. JOIN
    - 두 개 이상의 테이블을 관련 있는 컬럼을 통해 결합

*****************************************************/

-- 1-1. INNER JOIN
--      두 테이블의 교집합을 반환하는 SQL JOIN 유형
--      INNER JOIN에서 INNER 키워드는 생략 가능.

# 컬럼명이 같거나 다를 경우 ON으로 서로 연관있는 칼럼에 대한 조건을 작성하여 JOIN하는 경우
SELECT
    *
FROM
    tbl_menu a
JOIN tbl_category b ON a.category_code = b.category_code;


# 컬럼명이 같을 경우 USING으로 서로 연관있는 컬럼에 대한 조건을 작성하여 JOIN하는 경우
SELECT
    *
FROM
    tbl_menu a
JOIN tbl_category b using (category_code);


-- 1-2. LEFT JOIN
--      첫 번째(왼쪽)테이블의 모든 레코드와 두 번째(오른쪽) 테이블에서 일치하는 레코드를 반환하는 SQL JOIN 유형

SELECT
    *
FROM
    tbl_menu a
LEFT JOIN
    tbl_category b ON a.category_code = b.category_code;


-- 1-3. RIGHT JOIN
--      두 번째(오른쪽) 테이블의 모든 레코드와 첫 번째(왼쪽) 테이블에서 일치하는 레코드를 반환하는 SQL JOIN 유형

SELECT
    *
FROM
    tbl_menu a
RIGHT JOIN
    tbl_category b ON a.category_code = b.category_code;


-- 1-4. CROSS JOIN
--      두 테이블의 모든 가능한 조합을 반환하는 SQL JOIN 유형

SELECT
    *
FROM
    tbl_menu a
CROSS JOIN
    tbl_category b;


-- 1-5. SELF JOIN
--      같은 테이블 내에서 행과 행 사이의 관계를 찾기 위해 사용되는 SQL JOIN 유형

SELECT
    *
FROM
    tbl_category a
JOIN
    tbl_category b ON a.ref_category_code = b.category_code
WHERE
    a.ref_category_code IS NOT NULL;



/*****************************************************

  2. GROUPING
    - GROUP BY절은 결과 집합을 특정 열의 값에 따라 그룹화하는데 사용
    - HAVING은 GROUP BY 절과 함께 사용해야 하며 그룹에 대한 조건을 적용하는데 사용

*****************************************************/

-- 2-1. GROUPING

# GROUP BY를 활용한 메뉴가 존재하는 카테고리 그룹 조회
SELECT
    category_code
FROM
    tbl_menu
GROUP BY
    category_code;

# COUNT 함수 활용
SELECT
    category_code,
    COUNT(*)
FROM
    tbl_menu
GROUP BY
    category_code;

# SUM 함수 활용
SELECT
    category_code,
    SUM(menu_price)
FROM
    tbl_menu
GROUP BY
    category_code;

# AVG 함수 활용
SELECT
    category_code,
    AVG(menu_price)
FROM
    tbl_menu
GROUP BY
    category_code;

# 2개 이상의 그룹 생성
SELECT
    menu_price,
    category_code
FROM
    tbl_menu
GROUP BY
    menu_price, category_code;

# join과 함께 사용
SELECT
    a.category_code,
    b.category_name,
    AVG(a.menu_price)
FROM
    tbl_menu a
JOIN
    tbl_category b ON (a.category_code = b.category_code)
GROUP BY
    a.category_code, b.category_name;

-- 2-2. HAVING

# HAVING을 활용해 5번(중식) 카테고리부터 8번(커피) 카테고리까지의 메뉴 카테고리 번호 조회
SELECT
    category_code
FROM
    tbl_menu
GROUP BY
    category_code
HAVING
    category_code BETWEEN 5 AND 8;

-- 2-3. ROLLUP

# 컬럼 한 개를 활용한 ROLLUP
SELECT
    category_code,
    SUM(menu_price)
FROM
    tbl_menu
GROUP BY
    category_code
WITH ROLLUP;

# 컬럼 두 개를 활용한 ROLLUP
SELECT
    menu_price,
    category_code,
    SUM(menu_price)
FROM
    tbl_menu
GROUP BY
    menu_price,
    category_code
WITH ROLLUP;



/*****************************************************

  3. SUBQUERIES
    - SUBQUERY는 다른 쿼리(모든 SQL) 내에서 실행되는 쿼리
    - SUBQUERY의 결과를 활용해서 복잡한 MAINQUERY를 작성해 한번에 여러 작업을 수행

*****************************************************/

-- 3-1. SUBQUERY 활용

# 서브쿼리와 메인 쿼리를 활용한 다중열 결과 조회
# 서브쿼리
SELECT
    category_code
FROM
    tbl_menu
WHERE
    menu_name = '민트미역국';

# 메인쿼리
SELECT
    menu_code,
    menu_name,
    menu_price,
    category_code,
    orderable_status
FROM tbl_menu;

# 서브쿼리를 활용한 메인 쿼리
SELECT
    menu_code,
    menu_name,
    menu_price,
    category_code,
    orderable_status
FROM
    tbl_menu
WHERE
    category_code = (
        SELECT
            category_code
        FROM
            tbl_menu
        WHERE
            menu_name = '민트미역국'
    );

# SUBQUERY를 활용해 가장 많은 메뉴가 포함된 카테고리 조회
SELECT
    MAX(count)
FROM (
    SELECT
        COUNT(*) AS 'count'
    FROM
        tbl_menu
    GROUP BY
        category_code
    ) AS countmenu;

-- 3-2. 상관(상호연관) 서브쿼리
--      메인 쿼리가 서브쿼리의 결과에 영향을 주는 경우 상관 서브쿼리라고함.

# SUBQUERY를 활용해 카테고리별 평균 가격보다 높은 가격의 메뉴 조회
SELECT
    menu_code,
    menu_name,
    menu_price,
    category_code,
    orderable_status
FROM
    tbl_menu a
WHERE menu_price > (
        SELECT
            AVG(menu_price)
        FROM
            tbl_menu
        WHERE
            category_code = a.category_code
        );

-- 3-3. EXISTS
--      조회 결과가 있을 때 true 아니면 false

# EXISTS와 SUBQUERY를 활용한 메뉴가 있는 카테고리 조회
SELECT
    category_name
FROM
    tbl_category a
WHERE
    EXISTS(
        SELECT
            1
        FROM
            tbl_menu b
        WHERE
            b.category_code = a.category_code
    )
ORDER BY 1;

-- 3-4. CTE(Common Table Expressions)
--      FROM절에서만 사용 됨
--      인라인 뷰로 쓰인 서브쿼리(FROM 절에 쓰인 서브쿼리)를 미리 정의하고 메인쿼리가 심플해 질 수 있도록 사용하는 문법
WITH menucate AS (
    SELECT
        menu_name,
        category_name
    FROM
        tbl_menu a
    JOIN
        tbl_category b ON a.category_code = b.category_code
)
SELECT
       *
FROM
    menucate
ORDER BY
    menu_name;



/*****************************************************

  4. SET OPERATORS
    - SET 연산자는 두 개 이상의 SELECT문의 결과 집합을 결합하는데 사용
    - SET 연산자를 통해 결합하는 결과 집합의 컬럼이 동일해야 한다.

*****************************************************/

-- 4-1. UNION
--      두 개 이상의 SELECT 문의 결과를 결합하여 중복된 레코드를 제거한 후 반환하는 SQL 연산자이다.
SELECT
    menu_code,
    menu_name,
    menu_price,
    category_code,
    orderable_status
FROM
    tbl_menu
WHERE
    category_code = 10
 UNION
SELECT
    menu_code,
    menu_name,
    menu_price,
    category_code,
    orderable_status
FROM
    tbl_menu
WHERE
    menu_price < 9000;

-- 4-2. UNION ALL
--      두 개 이상의 SELECT 문의 결과를 결합하며 중복된 레코드를 제거하지 않고 모두 반환하는 SQL 연산자이다.
SELECT
    menu_code,
    menu_name,
    menu_price,
    category_code,
    orderable_status
FROM
    tbl_menu
WHERE
    category_code = 10
UNION ALL
SELECT
    menu_code,
    menu_name,
    menu_price,
    category_code,
    orderable_status
FROM
    tbl_menu
WHERE
    menu_price < 9000;

-- 4-3. INTERSECT
--      두 SELECT 문의 결과 중 공통되는 레코드만을 반환하는 SQL 연산자.
--      MySQL은 본래 기본적으로 INTERSECT를 제공하지 않는다. 하지만 INNER JOIN 또는 IN 연산자 활용해서 구현하는 것은 가능.

# INNER JOIN 사용
SELECT
    a.menu_code,
    a.menu_name,
    a.menu_price,
    a.category_code,
    a.orderable_status
FROM
    tbl_menu a
INNER JOIN (
        SELECT
            menu_code,
            menu_name,
            menu_price,
            category_code,
            orderable_status
        FROM
            tbl_menu
        WHERE
            menu_price < 9000
) b ON (a.menu_code = b.menu_code)
WHERE
    a.category_code = 10;

# IN 연산자 사용
SELECT
    a.menu_code,
    a.menu_name,
    a.menu_price,
    a.category_code,
    a.orderable_status
FROM
    tbl_menu a
WHERE
    category_code = 10
AND
    menu_code IN (
        SELECT
            menu_code
        FROM
            tbl_menu
        WHERE
            menu_price < 9000
    );

-- 4-4. MINUS
--      첫 번째 SELECT 문의 결과에서 두 번째 SELECT 문의 결과가 포함하는 레코드를 제외한 레코드를 반환하는 SQL 연산자
--      MySQL은 MINUS를 제공하지 않는다. 하지만 LEFT JOIN을 활용해서 구현하는 것은 가능
SELECT
    a.menu_code,
    a.menu_name,
    a.menu_price,
    a.category_code,
    a.orderable_status
FROM
    tbl_menu a
LEFT JOIN (
    SELECT
        menu_code,
        menu_name,
        menu_price,
        category_code,
        orderable_status
    FROM
        tbl_menu b
    WHERE
        menu_price < 9000
) b ON (a.menu_code = b.menu_code)
WHERE
    a.category_code = 10
AND
    b.menu_code IS NULL;

```
