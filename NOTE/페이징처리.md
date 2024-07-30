# 💥 페이징 처리

![그림1](https://github.com/user-attachments/assets/001c3117-828b-42a4-964d-22b226947923)

![그림2](https://github.com/user-attachments/assets/eb5635bd-5b1b-4666-994f-757ab6a1109a)

### ✔ 페이징에 필요한 요소들

![그림3](https://github.com/user-attachments/assets/f7f333a2-2bbc-4975-abec-14a2b940c917)


### ✔ Controller
```
@RequestMapping(value = "/notice/list.kh",method = RequestMethod.GET)
	public String showNoticeList(Model model
			,@RequestParam(value = "currentPage", defaultValue = "1") int currentPage 
			) {
			
		try {
			List<NoticeVO> nList = nService.selectNoticeList(currentPage);
			int totalCount = nService.getTotalCount();
			int naviCountPerPage = 10;
			int startNavi = ((currentPage-1)/naviCountPerPage)*naviCountPerPage + 1;
			int endNavi = startNavi + naviCountPerPage -1;
			int naviTotalCount = 0;
			
			if(totalCount%naviCountPerPage > 0) {
				naviTotalCount = totalCount / naviCountPerPage + 1;
			}else {
				naviTotalCount = totalCount / naviCountPerPage;
			}
			
			if(endNavi > naviTotalCount) {
				endNavi = naviTotalCount;
			}
			
			if(nList.size() > 0) {
					model.addAttribute("startNavi", startNavi);
					model.addAttribute("endNavi", endNavi);
					model.addAttribute("currentPage", currentPage);
					model.addAttribute("naviTotalCount", naviTotalCount);
					model.addAttribute("nList", nList);
					return "notice/list";
			}else {
				model.addAttribute("msg", "등록된 공지사항이 없습니다.");
				return "common/errorPage";
			}
		} catch (Exception e) {
			model.addAttribute("msg", e.getMessage());
			return "common/errorPage";
		}
	}
```
**1) jsp측에서 currentPage를 쿼리스트링으로 전달해준다. 이를 Controller에서 @RequestParam으로 받아오되, 초기값을 지정해주기 위해 defaultValue를 1로 지정해주어 오류를 방지한다.**   
**2) nService.selectNoticeList(currentPage)를 통해 현재페이지에 해당하는 list 객체를 가져온다.**     
**3) 페이지 네비게이션에 필요한 요소들**   
   - totalCount : getCount()메소드를 생성해 쿼리문을 통해 총 데이터의 개수를 구해준다.   
     (SELECT COUNT(*) FROM NOTICE_TBL)
        
   - naviCountPerPage : 페이지 네비게이션의 한페이지당 개수를 설정해준다. ( 만약 5라면 페이지 네비게이션 버튼 : 1 ~ 5)
        
   - startNavi : 페이지별 페이지 네비의 시작 숫자를 의미한다. 동적으로 부여해줘야 하므로 계산식인   
     ( ((currentPage-1)/naviCountPerPage)*naviCountPerPage + 1 ) 를 적용   
       
   - endNavi : 페이지별 페이지 네비의 끝 숫자를 의미한다. 동적으로 부여해줘야 하므로 계산식인   
     ( startNavi + naviCountPerPage -1 ) 를 적용
        
   - naviTotalCount : 페이지 네비의 총 개수를 의미한다. 이를 통해 endNavi가 naviTotalCount범위를 넘지 못하도록 제어한다.
        
    ```
     if(totalCount%naviCountPerPage > 0) {
				naviTotalCount = totalCount / naviCountPerPage + 1;
			}else {
				naviTotalCount = totalCount / naviCountPerPage;
			}
    ```
    ```
    if(endNavi > naviTotalCount) {
      endNavi = naviTotalCount;
    }
    ```   
**4) 각 값을 model.addAttribute()로 전달해준다.**
   
 
--------------------------
### ❓ Rowbounds 객체 ❓   
**RowBounds 클래스는 MyBatis라는 Java 기반의 데이터 매퍼 프레임워크에서 제공하는 기능 중 하나로, 페이징(paging) 기능을 지원하기 위해 사용된다.**

```
int offset = 10; // 조회를 시작할 위치 (10번째 데이터부터 시작)
int limit = 20;  // 조회할 데이터의 개수 (최대 20개의 데이터)
RowBounds rowBounds = new RowBounds(offset, limit);

session.selectOne("",null,rowBounds); 로 사용하고자 하는 매퍼 메서드의 인자로 전달해주어야 한다.
```
------------------------------

### ✔ store에서 rowBounds 생성 및 session 메소드의 인자로 전달    
```
public List<NoticeVO> selectNoticeList(SqlSession session, int currentPage) {
		int limit = 10;
		int offset = (currentPage-1)*limit;
		RowBounds rowBounds = new RowBounds(offset, limit);
		List<NoticeVO> nList = session.selectList("NoticeMapper.selectList",null,rowBounds);
		return nList;
	}
```


### ✔ list.jsp 의 테이블   
**list.jsp에 반복문을 통해 list데이터를 출력**
```
<c:forEach var="list" items="${nList }">
	<tr>
		<td>${list.noticeNo }</td>
		<td><a href="/notice/detail.kh?noticeNo=${list.noticeNo }&currentPage=${currentPage}">${list.noticeSubject }</a></td>
		<td>${list.noticeWriter }</td>
		<td>${list.noticeDate }</td>
		<td>${list.viewCount }</td>
	</tr>
</c:forEach>
```

### ✔ 페이지 네비게이션
```
<td colspan="5">
	<c:if test="${currentPage ne 1 }">
		<c:if test="${startNavi ne 1 }">
			<a href="/notice/list.kh?currentPage=${startNavi-10 }">이전페이지</a>
		</c:if>
		<a href="/notice/list.kh?currentPage=${currentPage-1 }">이전</a>
	</c:if>
	
	<c:forEach begin="${startNavi }" end="${endNavi }" var="i">
		<c:if test="${i eq currentPage }">
			<span style="font-size: 20px;">${i }</span>
		</c:if>
		
		<c:if test="${i ne currentPage }">
			<a href="/notice/list.kh?currentPage=${i }">${i }</a>
		</c:if>
	</c:forEach>
	
	<c:if test="${currentPage ne naviTotalCount }">
		<a href="/notice/list.kh?currentPage=${currentPage+1 }">다음</a>
		<c:if test="${endNavi ne naviTotalCount }">
			<a href="/notice/list.kh?currentPage=${startNavi+10 }">다음페이지</a>
		</c:if>
	</c:if>
	
</td>
```







------oracle-----
