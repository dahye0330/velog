<p>오늘은 HTML, CSS, JavaScript를 이용해 간단한 엑셀 파일 업로드 기능을 가진 프론트엔드 페이지를 만들어보았다. 이 프로젝트는 회사에서 불펴함을 느낀 엑셀 파일 파싱 과정을 자동화하기 위헤 시작했다.</p>
<h2 id="1-프로젝트-개요">1. 프로젝트 개요</h2>
<p>이번 프로젝트의 목표는 사용자가 엑셀 파일을 업로드할 수 있는 기능과 함께 추가적인 명령어를 입력할 수 있는 UI를 만드는 것이었다. 전체 페이지는 다음과 같은 요소들로 구성했다:</p>
<ul>
<li><strong>엑셀 파일 업로드 인풋</strong>: 사용자가 엑셀 파일(.xls, .xlsx)을 업로드할 수 있도록 구현.</li>
<li><strong>명령어 입력 필드</strong>: 빌드 명령어를 입력할 수 있는 텍스트 필드.</li>
<li><strong>업로드 버튼</strong>: 사용자가 업로드한 파일과 명령어를 제출할 수 있는 버튼.</li>
</ul>
<p>이 모든 UI 요소는 HTML로 구성하고, CSS를 이용해 깔끔한 디자인을 추가했다. 또한, JavaScript로 간단한 검증과 제출 동작을 구현했다.</p>
<p>참고: 추가 명령어 입력 필드가 필요한 이유는 빌드할 프로젝트가 달라질 때마다 커스텀할 수 있게 하기 위함이다.</p>
<hr />
<h2 id="2-작업-과정">2. 작업 과정</h2>
<p><a href="https://github.com/dahye0330/build-linker/commit/8418eee78b26bafe5073ec70d043cc660ad7e92d">작업한 commit 링크</a></p>
<p><strong>1) HTML 구조 작성하기</strong><br />우선, HTML로 페이지의 기본 구조를 만들었다. 파일 업로드와 명령어 입력 필드를 각각 <code>&lt;input&gt;</code> 태그로 정의하고, 적절한 레이블과 버튼을 추가했다. 레이블을 한글로 작성해 사용자 친화성을 높였다. 아래는 엑셀 파일 업로드 필드와 버튼을 포함한 코드 예제다:</p>
<pre><code class="language-html">&lt;div class=&quot;form-group&quot;&gt;
  &lt;label for=&quot;excelFile&quot;&gt;엑셀 파일 업로드:&lt;/label&gt;
  &lt;input type=&quot;file&quot; id=&quot;excelFile&quot; accept=&quot;.xls,.xlsx&quot;&gt;
&lt;/div&gt;</code></pre>
<p><code>accept</code> 속성을 이용해 업로드할 파일의 형식을 제한하는 것도 새로운 배움이었다.</p>
<p><strong>2) CSS로 디자인 꾸미기</strong><br />CSS를 활용해 페이지를 깔끔하고 직관적으로 보이도록 스타일링했다. 컨테이너에 <code>box-shadow</code>와 <code>border-radius</code>를 추가해 조금 더 부드럽고 현대적인 느낌을 주었다. 버튼 색상은 초록색으로 설정했고, 호버 시 색이 살짝 변하도록 CSS로 구현했다:</p>
<pre><code class="language-css">.upload-button {
  background-color: #28a745; /* 초록색 */
}

.upload-button:hover {
  background-color: #218838; /* 더 어두운 초록 */
}</code></pre>
<p>버튼 디자인을 사용자 경험(UX) 관점에서 신경 썼던 점이 개인적으로 만족스러웠다.</p>
<p><strong>3) JavaScript로 기본 로직 구현</strong><br />JavaScript를 이용해 사용자가 파일을 업로드하지 않았거나 명령어를 입력하지 않았을 때 경고를 표시하도록 로직을 추가했다. 예를 들어, 아래와 같은 간단한 검증 코드를 작성했다:</p>
<pre><code class="language-javascript">if (!file) {
  alert('엑셀 파일을 선택해주세요.');
  return;
}</code></pre>
<p>이와 함께 파일과 입력값을 <code>console.log</code>로 출력해 개발 중 디버깅을 할 수 있도록 했다.</p>
<hr />
<h2 id="3-작업하며-배운-점">3. 작업하며 배운 점</h2>
<p>이번 프로젝트를 통해 <strong>HTML, CSS, JavaScript의 기본적인 사용법</strong>을 한 단계 더 깊이 이해할 수 있었다. 특히 다음과 같은 점들이 인상 깊었다:</p>
<ul>
<li><strong>파일 업로드와 입력값 검증</strong>: HTML의 <code>&lt;input&gt;</code> 태그와 JavaScript를 이용해 간단한 유효성 검사를 구현할 수 있었다.</li>
<li><strong>CSS로 깔끔한 UI 만들기</strong>: 작은 디테일(예: 버튼 색상 변화, 박스 그림자)만으로도 사용성 높은 UI를 만들 수 있었다.</li>
<li><strong>사용자 경험(UX) 개선</strong>: 레이블을 한글로 작성하거나 명확한 에러 메시지를 제공하는 것만으로도 사용자 친화적인 페이지가 된다.</li>
</ul>
<hr />
<h2 id="4-개선하고-싶은-점">4. 개선하고 싶은 점</h2>
<p>아직 프론트엔드에 익숙하지 않아 개선할 점도 많이 보였다:</p>
<ul>
<li><strong>업로드한 파일 처리</strong>: JavaScript에서 파일 내용을 읽고 서버로 전송하는 기능을 추가할 필요가 있다.</li>
<li><strong>반응형 디자인</strong>: 모바일에서도 적절하게 동작할 수 있도록 미디어 쿼리를 추가하면 좋을 것 같다.</li>
<li><strong>Vue.js와 같은 프레임워크 활용</strong>: 프로젝트가 복잡해지면 Vue.js나 React 같은 프레임워크를 도입해 유지보수를 쉽게 할 수 있도록 하고 싶다.</li>
</ul>
<hr />
<h2 id="5-마무리">5. 마무리</h2>
<p>이번 프로젝트는 작은 규모의 작업이었지만, 프론트엔드 개발의 기본적인 구성 요소와 로직을 이해하는 데 큰 도움이 되었다. 앞으로 서버와의 연동이나 더 고급 CSS 기술을 활용해 이 페이지를 더욱 발전시켜볼 계획이다.</p>
<hr />