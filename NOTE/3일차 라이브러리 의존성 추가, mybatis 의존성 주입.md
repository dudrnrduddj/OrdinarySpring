## maven repository
### pom.xml 에 의존성 추가
1. mybatis
2. mybatis-spring
3. ojdbc8
4. commons-dbcp
5. spring-jdbc


## mybatis 이용 (자바 프로젝트 -> spring MVC 프로젝트) 차이
1. 
  - **Before**
  
```
<!--mybatis-config.xml-->
<dataSource type="POOLED">
<property name="driver" value="oracle.jdbc.driver.OracleDriver"/>
<property name="url" value="jdbc:oracle:thin:@localhost:1521:xe"/>
<property name="username" value="ORDINARYJDBC"/>
<property name="password" value="ORDINARYJDBC"/>
</dataSource
```
  - **After**
```
<!--root-context.xml-->
<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource">
  <property name="driverClassName" value="oracle.jdbc.driver.OracleDriver"></property>
  <property name="url" value="jdbc:oracle:thin:@localhost:1521:xe"></property>
  <property name="username" value="ORDINARYSPRING"></property>
  <property name="password" value="ORDINARYSPRING"></property>
</bean>
```

2.
  - **Before**
```
<!--mybatis-config.xml-->
public class SqlSessionTemplate {
  public static SqlSession getSqlSession() {
    SqlSession session = null;
    String resource = "mybatis-config.xml";
    try {
      InputStream is = Resources.getResourceAsStream(resource);
      SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder(); // 공장 기술자
      SqlSessionFactory sessionFactory = builder.build(is); // 공장 만들어짐(스트림 객체 전달)
      session = sessionFactory.openSession(true); // 연결 생성 			
    } catch (IOException e) {
      e.printStackTrace();
    }
    return session;
  }
}
```

  - **After**
```
<!--root-context.xml-->
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
  <property name="configLocation" value="classpath:mybatis-config.xml"></property>
  <property name="dataSource" ref="dataSource"></property>
</bean>
<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
  <constructor-arg ref="sqlSessionFactory"></constructor-arg>
</bean>
```

*의존성 주입을 이용해 mybatis 설정 및 session 생성*
