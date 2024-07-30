## 💥 페이징 처리를 위한 PL-SQL
```
DECLARE
    V_USERID VARCHAR2(200);
BEGIN
    FOR N IN 1..326
    LOOP
        V_USERID := '1'||LPAD(SEQ_NOTICE_NO.NEXTVAL, 9, '0');
        INSERT INTO NOTICE_TBL
        VALUES(V_USERID,'공지사항'||N||'번째',N||'번째 공지내용','admin',DEFAULT, DEFAULT, DEFAULT);
    END LOOP;
    COMMIT;
END;
/
```
와 같이 PL-SQL을 이용해 더미 데이터를 INSERT 시켜줄 수 있었다.   
 
----------------------------------------------
## 💥 페이징 처리 쿼리문

### 💥 ROWNUM 키워드 이용하기
 
#### ROWNUM 이란 ❓
 - ROWNUM은 Oracle 데이터베이스에서 사용되는 가상 컬럼으로, **쿼리 결과에 대한** 행 번호를 반환한다.   
 - ROWNUM은 **쿼리 결과의 각 행에 대해** 고유한 순서 번호를 부여하며, 이 번호는 1부터 시작하여 쿼리 결과에 따라 증가한다.   
   
**✔ ex)1. ROWNUM 사용**
- 조회된 순서대로 순번을 매기는 경우
   ```
   SELECT ROWNUM, a.* FROM emp a;
   ```
- ORDER BY를 사용하면 순번이 뒤섞이므로 정렬된 서브쿼리 결과에 ROWNUM을 매겨야 한다.
  ```
  SELECT ROWNUM, x.* FROM (SELECT a.* FROM emp a ORDER BY a.name) x;
  ```
     
  **-> 정렬된 쿼리결과에 rownum을 부여!! ( rownum부여후 정렬과 엄연히 다름 )**

   
**✔ ex) 2. ROW_NUMBER() 함수 사용**
- ORDER BY 된 결과에 순번을 매길때에는 ROWNUM 보다 ROW_NUMBER() 함수가 더 편하다.
```
SELECT ROW_NUMBER() OVER(ORDER BY a.jobm a.ename) row_num, a.* FROM emp a ORDER BY a.job, a.ename;
```
   
```
SELECT ROWNUM R, N.* FROM (SELECT T.* FROM NOTICE_TBL T ORDERY BY NOTICE_NO DESC) N WHERE R BETWEEN #{startNo} AND #{endNo}
```    
와 같이 페이징처리를 할 때 쿼리문을 이용할 수 있었다. ( startNo, endNo는 각각 페이지별 게시물의 시작번호와 끝번호 )
