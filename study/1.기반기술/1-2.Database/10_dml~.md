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



/*****************************************************

  4. DDL
    - DDL(Data Definition Language)는 데이터베이스의 스키마를 정의하거나 수정하는 데 사용되는 SQL의 한 부분

*****************************************************/

-- 스키마 : 테이블의 구조 및 제약조건 전반을 아울러 지칭하는 말

-- 4-1. CREATE
--      테이블 생성을 위한 구문
--      IF NOT EXISTS를 적용하면 기존에 존재하는 테이블이라도 에러가 발생하지 않는다.

# 테이블 생성
/*
CREATE TABLE [IF NOT EXISTS] 테이블명 (
  컬럼명1 데이터타입 [제약조건],
  컬럼명2 데이터타입 [제약조건],
  ...
);
*/

CREATE TABLE IF NOT EXISTS tb1 (
    pk INT PRIMARY KEY, -- 컬럼 레벨에서  제약조건 추가
    fk INT,
    col1 VARCHAR(255),
    CHECK(col1 IN ('Y', 'N')) -- 테이블 레벨에서 제약조건 추가
) ENGINE=INNODB;

# 테이블 구조 확인
DESCRIBE tb1;
DESC tb1;

# INSERT테스트
INSERT INTO tb1 VALUES (1, 10, 'Y');

SELECT * FROM tb1;

-- 4-2. AUTO_INCREMENT
--      INSERT 시 PRIMARY키에 해당하는 컬럼에 자동으로 번호를 발생(중복되지 않게)시켜 저장할 수 있다.

-- 4-3. ALTER
--      테이블에 추가/변경/수정/삭제하는 모든 것은 ALTER 명령어를 사용해 적용

# 열 추가
ALTER TABLE tb1
ADD col2 INT NOT NULL;

# 열 삭제
ALTER TABLE tb1
DROP COLUMN col2;

# 열 변경
ALTER TABLE tb1
CHANGE COLUMN fk change_fk INT NOT NULL;

# 컬럼 여러개 추가
ALTER TABLE tb1
ADD col3 DATE NOT NULL,
ADD col4 TINYINT NOT NULL;

-- 4-4. DROP
--      테이블을 삭제하기 위한 구문
DROP TABLE IF EXISTS tb1;

-- 4-5. TRUNCATE
--      데이터를 다 삭제할 경우 행마다 하나씩 지워지는 DELETE보다 DROP이후 바로 테이블을 재생성 해주는 TRUNCATE가 훨씬 효율적으로 한번에 테이블을 초기화 시켜준다.
--      TRUNCATE로 데이터 삭제 시 ROLLBACK 불가
--      또한 AUTO_INCREMENT 컬럼이 있는 경우 시작 값도 0으로 초기화가 된다.



/*****************************************************

  5. CONSTRAINTS
    - CONSTRAINT는 제약 조건으로 테이블에 데이터가 입력되거나 수정될 때의 규칙을 정의
    - 데이터 무결성을 보장하는데 도움이 된다.

*****************************************************/

-- 5-1. NOT NULL
--      NULL값을 허용하지 않는 제약 조건

-- 5-2. UNIQUE
--      중복값 허용하지 않는 제약조건

-- 5-3. PRIMARY KEY
--      테이블에서 한 행의 정보를 찾기 위해 사용 할 컬럼을 의미
--      NOT NULL + UNIQUE 제약조건의 의미
--      한 테이블당 한 개만 설정할 수 있음
--      컬럼 레벨, 테이블 레벨 둘 다 설정 가능함

-- 5-4. FOREIGN KEY
--      참조 무결성을 위배하지 않기 위해 사용
--      참조(REFERENCES)된 다른 테이블에서 제공하는 값만 사용할 수 있음
--      FOREIGN KEY 제약조건에 의해서 테이블 간의 관계(RELATIONSHIP)가 형성 됨
--      제공되는 값 외에는 NULL을 사용할 수 있음
DROP TABLE IF EXISTS user_foreignkey1;
CREATE TABLE IF NOT EXISTS user_foreignkey1 (
    user_no INT PRIMARY KEY,
    user_id VARCHAR(255) NOT NULL,
    user_pwd VARCHAR(255) NOT NULL,
    user_name VARCHAR(255) NOT NULL,
    gender VARCHAR(3),
    phone VARCHAR(255) NOT NULL,
    email VARCHAR(255),
    grade_code INT ,
    FOREIGN KEY (grade_code)
		REFERENCES user_grade (grade_code)
) ENGINE=INNODB;

INSERT
  INTO user_foreignkey1
(user_no, user_id, user_pwd, user_name, gender, phone, email, grade_code)
VALUES
(1, 'user01', 'pass01', '홍길동', '남', '010-1234-5678', 'hong123@gmail.com', 10),
(2, 'user02', 'pass02', '유관순', '여', '010-777-7777', 'yu77@gmail.com', 20);

SELECT * FROM user_foreignkey1;

-- 5-5. DEFAULT
--      컬럼에 null 대신 기본 값 적용
--      컬럼 타입이 DATE일 시 current_date만 가능
--      컬럼 타입이 DATETIME일 시 current_time과 current_timestamp, now() 모두 사용 가능

```
