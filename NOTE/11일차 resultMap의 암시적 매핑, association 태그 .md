# 💥 mapper에서 자동 키 생성 후 객체에 반영 후 반환 (useGeneratedKeys="true")

```
<insert id="insertNotice" useGeneratedKeys="true">
		<selectKey order="BEFORE" resultType="_int" keyProperty="noticeNo">
			SELECT SEQ_NOTICE_NO.NEXTVAL FROM DUAL
		</selectKey>
		INSERT INTO NOTICE_TBL VALUES(#{noticeNo}, #{noticeSubject}, #{noticeContent}, #{noticeWriter}, DEFAULT, DEFAULT, DEFAULT)
</insert>
```

 - **useGeneratedKeys :** 
삽입 후 MyBatis에게 자동 생성된 키 값을 Java객체에 반영하라고 지시한다.   
객체는 매퍼에서 SQL문을 호출할 때 전달되는 파라미터 객체와 'keyProperty' 속성에 의해 결정된다.   
   
 - keyProperty : MyBatis가 자동 생성된 키 값을 이 파라미터 객체의 필드에 설정하도록 지시한다.   
   
## ✔ 정리   
1. **SELECT SEQ_NOTICE_NO.NEXTVAL FROM DUAL 쿼리문을 실행하여 noticeNo값을 생성**   
2. **값을 해당 객체의 noticeNo필드에 설정**   
3. **INSERT INTO NOTICE_TBL VALUES(#{noticeNo}, #{noticeSubject}, #{noticeContent}, #{noticeWriter}, DEFAULT, DEFAULT, DEFAULT) 문이 실행**   
 (단, #{noticeNo}는 방금 생성된 시퀀스 값을 대체)
4. **useGeneratedKeys="true"로 인해, 삽입 후 데이터베이스에서 생성된 값이 있을 경우, 그 값을 객체에 반영**   


# 💥 클래스에서 다른 클래스를 필드로 가질 때 mapper.xml에서의 객체 매핑 + association 태그   

   
 - NoticeVO.java   
```
public class NoticeVO{
	private ...
	...
	
	private NoticeFileVO noticeFile; // noticeVO에서 필드로 NoticeFileVo객체를 갖음.
}
```   
   

 - mapper.xml   
```
<resultMap id="notice_resultmap" type="NoticeVO">
	<id property="noticeNo" column="NOTICE_NO"/>
	<association property="noticeFile"
		javaType="NoticeFileVO"
		select="selectNoticeFile"
		column="NOTICE_NO"/>
</resultMap>
	
	
<select id="selectNoticeFile" resultType="NoticeFileVO">
	SELECT * FROM NOTICE_FILE_TBL WHERE NOTICE_NO = #{noticeNo}
</select>

<select id="selectOne" resultMap="notice_resultmap">
	SELECT * FROM NOTICE_TBL WHERE NOTICE_NO = #{noticeNo}
</select>
```   
   
1. **selectOne 메서드 호출**:
 - 클라이언트 코드에서 selectOne 메서드를 호출할 때, noticeNo를 파라미터로 전달한다.     
 - 이 호출로 인해 MyBatis는 NOTICE_TBL에서 해당 noticeNo에 해당하는 레코드를 조회하는 SQL을 실행한다.   

2. **resultMap을 사용한 매핑**:
 - selectOne 쿼리 결과는 notice_resultmap을 통해 Java 객체로 매핑된다.   
 - notice_resultmap은 NoticeVO 객체와 데이터베이스 컬럼 간의 매핑 규칙을 정의한다.   
 - id property="noticeNo" column="NOTICE_NO": 이 부분은 NOTICE_NO 컬럼 값을 NoticeVO의 noticeNo 속성에 매핑한다.   
 - association property="noticeFile": 이 부분은 NoticeVO 객체의 noticeFile 속성에 대해 연관된 데이터를 설정하는 방법을 정의한다.   

3. **selectNoticeFile 서브쿼리의 자동 실행**:
 - association 맵핑에서 select="selectNoticeFile"로 정의된 부분은 NOTICE_NO를 사용하여 NOTICE_FILE_TBL에서 파일 정보를 조회한다.   
 - 이 과정은 자동으로 수행되며, NOTICE_TBL의 NOTICE_NO 값이 selectNoticeFile 쿼리로 전달된다.   
 - selectNoticeFile 쿼리는 NOTICE_NO에 해당하는 파일 정보를 NoticeFileVO 객체에 매핑한다.   

4. **최종 객체 반환**:
 - 모든 매핑이 완료된 후, NoticeVO 객체는 공지사항 정보와 연관된 파일 정보를 포함하여 클라이언트 코드로 반환된다.   
 

# ❓ 의문점

**resultMap에 id태그만 명시하였는데 어떻게 다른 필드들이 컬럼에 매핑될 수 있을까?**   

Mybatis에서 resultMap은 SQL 쿼리 결과를 Java 객체로 매핑하는 방법을 정의하는 역할을 한다.   

 - **id 태그 :** 기본 키(Primary Key) 필드를 매핑하기 위해 사용된다. MyBatis는 이 태그가 지정된 필드를 특별하게 취급하여 객체의 id로 간주한다.   
 - **나머지 필드들 :** id 태그 외에 resultMap 내에 특정 필드에 대한 정의가 없더라도, MyBatis는 SQL 쿼리 결과의 컬럼 이름과 Java 객체의 필드 이름이 일치할 경우 자동으로 매핑을 시도한다. 이를 **"암시적 매핑"** 이라고 부른다.   
    
 **즉, resultMap에서 id 태그만 정의하더라도 나머지 필드는 MyBatis의 암시적 매핑 기능에 의해 자동을 매핑된다.**  
