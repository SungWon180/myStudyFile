```sql

/*****************************************************

  1. DML(Data Manipulation Language)
    - 데이터 조작언어, 테이블에 값을 삽입하거나 수정하거나 삭제하는(데이터베이스 내의 데이터를 조작하는데 사용하는) SQL의 한 부분

*****************************************************/

-- 1-1. INSERT
--      새로운 행을 추가하는 구문이다.
--      테이블의 행의 수가 증가한다.
INSERT
INTO tbl_menu
VALUES (NULL, '바나나해장국',8500,4,'Y');

# NULL 허용 가능한(NULLABLE) 컬럼이나 AUTO_INCREMENT가 있는 컬럼을 제외하고 INSERT하고 싶은 데이터 컬럼을 지정해서 INSERT 가능
INSERT
INTO tbl_menu
       (menu_name, menu_price, category_code, orderable_status)
VALUES ('초콜릿죽', 6500, 7, 'Y');

# MULTI INSERT
INSERT
INTO tbl_menu
VALUES
(null, '참치맛아이스크림', 1700, 12, 'Y'),
(null, '멸치맛아이스크림', 1500, 11, 'Y'),
(null, '소시지맛커피', 2500, 8, 'Y');

-- 1-2. UPDATE
--      테이블에 기록된 컬럼의 값을 수정하는 구문
--      테이블의 전체 행 갯수는 변화가 없다.
UPDATE
    tbl_menu
SET
    category_code = 7,
    menu_name = '딸기맛붕어빵'
WHERE
    menu_code = 24;

# SUBQUERY를 활용
UPDATE
    tbl_menu
SET
    category_code = 6
WHERE
    menu_code = (
        SELECT
            menu_code
        FROM
            tbl_menu
        WHERE
            menu_name = '초콜릿죽'
    );

-- 1-3. DELETE
--      테이블의 행을 삭제하는 구문
--      테이블의 행의 갯수가 줄어든다.

# LIMIT을 활용한 행 삭제
DELETE
FROM
    tbl_menu
ORDER BY
    menu_price
LIMIT 2;

# WHERE절을 활용한 단일 행 삭제
DELETE
FROM
    tbl_menu
WHERE
    menu_code = 24;

# 해당 테이블 전체 행 삭제
DELETE
FROM
    tbl_menu
WHERE
    1 = 1;

-- 1-4. REPLACE
--      INSERT 시 PRIMARY KEY 또는 UNIQUE KEY가 충돌이 발생할 수 있다면
--      REPLACE를 통해 중복된 데이터를 덮어 쓸 수 있다.
REPLACE
INTO
    tbl_menu
VALUES (17, '참기름소주', 5000, 10, 'Y');

# INTO는 생략 가능
REPLACE
    tbl_menu
VALUES (17, '참기름소주', 6500, 10, 'Y');

# UPDATE 시 WHERE 구문 없이 UPDATE가 가능
REPLACE
    tbl_menu
SET
    menu_code = 2,
    menu_name = '우럭쥬스',
    menu_price = 2000,
    category_code = 9,
    orderable_status = 'N';



/*****************************************************

  2. TRANSACTION
    - TRANSACTION은 데이터 베이스에서 한 번에 수행되는 작업의 단위
    - 시작, 진행, 종료 단계를 가지며 만약 중간에 오류가 발생하면 롤백(시작 이전 단계로 되돌리는 작업)을 수행하고
      데이터 베이스에 제대로 반영하기 위해서는 커밋(이후 롤백이 되지 않음)을 진행한다.
    - MySQL은 기본적으로 자동 커밋 설정이 되어 있어(롤백이 안됨). 롤백을 하기 위해서는 자동 커밋 설정을 해제해 주어야 한다.

*****************************************************/

-- 2-1. Transaction 활용
--      MySQL은 기본적으로 commit이 자동으로 되므로 수동으로 조절하고 싶다면 autocommit 설정을 바꿔 주어야 한다.

#  autocommit 활성화
SET autocommit = 1;
-- 또는
SET autocommit = ON;

# autocommit 비활성화
SET autocommit = 0;
-- 또는
SET autocommit = OFF;

--      START TRANSACTION 구문을 작성하고 DML 작업 수행 후 COMMIT 또는 ROLLBACK을 하면 된다.
--      COMMIT 이후에는 ROLLBACK을 해도 ROLLBACK이 적용되지 않는다.
START TRANSACTION;

SELECT * FROM tbl_menu;
INSERT INTO tbl_menu VALUES (null, '바나나해장국', 8500, 4, 'Y');
UPDATE tbl_menu SET menu_name = '수정된 메뉴' WHERE menu_code = 5;
DELETE FROM tbl_menu WHERE menu_code = 7;

-- COMMIT;
ROLLBACK;



/*****************************************************

  3. DATA TYPES
    - MariaDB는 여러 가지 데이터 유형을 지원(문자열, 숫자, 날짜, 시간)한다.
    - 적절한 데이터 유형을 정의하면 데이터 저장 공간을 효율적으로 사용하고 데이터 입력의 유효성 검사에도 도움이 된다.

*****************************************************/

-- 3-1. 숫자 데이터 형식
--      정수 또는 실수 등의 숫자를 표현한다.
--      FLOAT이나 DOUBLE형은 큰 범위의 숫자를 저장할 수 있지만 정확하지 않은 근사치를 저장한다.
--      실수 형을 저장하고 싶어도 DECIMAL을 사용하는 것이 바람직하다.

# INT OR INTEGER -> 4byte -> 정수
# DECIMAL -> 5~17byte -> 전체 자릴수와 소수점 이하 자릿수를 가짐

-- 3-2. 문자 데이터 형식
--      CHAR는 고정길이 문자형으로 자릿수가 불변이다.
--      VARCHAR는 가변길이 문자형으로 자릿수가 가변이다. (큰 자릿수를 설정해도 저장할 공간을 효율적으로 사용할 수 있다.)
--      CHAR 형식으로 설정하는 것이 INSERT/UPDATE 시에 일반적으로 더 좋은 성능을 발휘한다.
--      대용량 데이터는 TEXT계열의 데이터 형식으로 저장
--      BLOB(Binary Large Object)은 사진 파일, 동영상 파일, 문서 파일 등의 대용량 이진 데이터를 저장

# CHAR(n) -> 1 ~ 255byte -> 고정길이 문자형, CHAR만 쓰면 CHAR(1)과 동일
# VARCHAR(n) -> 1 ~ 65535byte -> 가변길이 문자형
# TEXT -> 1 ~ 65535byte -> N 크기의 TEXT 데이터 값

```
