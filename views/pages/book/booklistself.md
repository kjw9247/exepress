<div class="row mt-5 justify-content-end">
  <div class="col-6 col-md-4">
<!-- 검색 키워드 전송하기  -->
      <form name="frm">
        <div class="input-group">
          <input type="text" name="query" class="form-control" />
          <button class="btn btn-success">검색</button>
        </div>
      </form>
<!-- 검색 키워드 전송하기  -->
  </div>
</div>
<hr />
<!-- 카카오 도서검색 결과를 출력할 공간만 마련해둠 -->
<div class="row" id="list_book"></div>
<!-- 카카오 도서검색 결과를 출력할 공간만 마련해둠 -->

<!-- 도서 목록 템플릿 -->
<script type="text/x-handlebars-template" id="temp-book">
  {{#each documents}}
    <div class="col-6 col-md-4 col-lg-2">
      <div class="card my-2">
        <div class="card-body text-center">
          <img src="{{image thumbnail}}" alt="도서이미지" style="cursor:pointer; width:80%" />
          <div class="ellipsis mt-2">{{title}}</div>
        </div>
        <div class="card-footer text-center" style="font-size:0.9rem;">
          {{format price}}
          <span class="cart ms-3" book="{{book @this}}" style="cursor:pointer; color:green;">CART</span>
        </div>
      </div>
    </div>
  {{/each}}
</script>
<!-- 도서 목록 템플릿 -->
<!-- Handlebards 헬퍼 등록 -->
<script>
  Handlebars.registerHelper("format", function(price) {
    return price.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ',') + '원';
  });
  Handlebars.registerHelper("image", function(thum){
    return thum || "https://placehold.co/120x174"
  })
  Handlebars.registerHelper("book", function(book){
    return JSON.stringify(book)
  })
</script>
<!-- 페이지 네비게이션 버튼 -->
<div class="text-center mt-2">
  <button class="btn btn-primary btn-sm px-5" id="prev">이전</button>
  <span id="page" class="px-3">1/10</span>
  <button class="btn btn-primary btn-sm px-5" id="next">다음</button>
</div>
<!-- 페이지 네비게이션 버튼 -->
<!-- 데이터 가져오기[Back-End] -->
<script type="module">
  //파이어베이스 API활용 - 장바구니 구현, 공지사항 구현, 게시판구현
  import { app } from '/javascripts/firebase.js'
  import { getDatabase } from "https://www.gstatic.com/firebasejs/11.7.3/firebase-database.js"
  const db = getDatabase(app)
  console.log(db);
  let page = 1
  let query

  //검색 폼 제출하기
document.forms['frm'].addEventListener('submit', (e) => {
  e.preventDefault() // submit 이슈 방어 코드
  // id가 아니라 name을 접근할 때
  query = document.forms['frm'].query.value
  console.log(query);
  getBookList()
})


  //사용자가 입력한 책제목 키워드 저장하기 - 전역적으로 사용 - 변수 선언 위치

  //도서검색 리스트 조회 함수 구현
  //카카오 도서검색 URL요청하기 - 비동기 처리
  const lisetEl = document.querySelector('#list_book')
  const getBookList = () => {
    const book_url = new URL('https://dapi.kakao.com/v3/search/book')
    book_url.searchParams.set('target', 'title')
    book_url.searchParams.set('query', 'query')
    book_url.searchParams.set('page', page)
    book_url.searchParams.set('size', 6)

    //카카오 서버와 통신하기
    fetch(book_url, {
      headers: { 'Authorization':'KakaoAK ac8481e5c39e26462dda5a549b1aaa39' }
    })
    .then(res => res.json())
    .then(result => {
      const imsi = Handlebars.compile(document.querySelector('#temp-book').innerHTML)
      lisetEl.innerHTML = imsi(result)
    })
    .catch((error) => console.error(error));

  }//end of getBookList
  getBookList()

</script>
<!-- 데이터 가져오기[Back-End] -->