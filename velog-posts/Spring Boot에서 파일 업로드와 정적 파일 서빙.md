<p>오늘은 Spring Boot에서 파일 업로드와 정적 파일 관리, 그리고 관련된 문제 해결 방법을 배우며 개발 경험을 쌓았습니다. 아래는 오늘 배운 내용을 바탕으로 정리한 내용입니다. (<a href="https://github.com/dahye0330/build-linker/commit/a14ad67232c7755793c3894af6537b2ee89de66b">git commit</a>)</p>
<hr />
<h4 id="1-html-파일-업로드-기능-구현">1. <strong>HTML 파일 업로드 기능 구현</strong></h4>
<p>먼저, 사용자로부터 파일과 추가 데이터를 입력받아 서버로 전송하는 HTML 양식을 작성했습니다.</p>
<pre><code class="language-html">&lt;div class=&quot;upload-container&quot;&gt;
    &lt;h1&gt;Excel File Uploader&lt;/h1&gt;
    &lt;div class=&quot;form-group&quot;&gt;
        &lt;label for=&quot;excelFile&quot;&gt;엑셀 파일 업로드:&lt;/label&gt;
        &lt;input type=&quot;file&quot; id=&quot;excelFile&quot; accept=&quot;.xls,.xlsx&quot;&gt;
    &lt;/div&gt;
    &lt;div class=&quot;form-group&quot;&gt;
        &lt;label for=&quot;buildCommand&quot;&gt;빌드 명령어 입력:&lt;/label&gt;
        &lt;input type=&quot;text&quot; id=&quot;buildCommand&quot; placeholder=&quot;Enter build command&quot;&gt;
    &lt;/div&gt;
    &lt;button class=&quot;upload-button&quot; onclick=&quot;uploadFile()&quot;&gt;Upload&lt;/button&gt;
&lt;/div&gt;</code></pre>
<p>그리고 JavaScript로 파일 업로드를 처리하는 코드를 작성했습니다. 이 코드는 파일과 명령어를 <code>FormData</code> 객체로 묶어서 서버에 전송합니다.</p>
<pre><code class="language-javascript">function uploadFile() {
    const fileInput = document.getElementById('excelFile');
    const buildCommandInput = document.getElementById('buildCommand');

    const file = fileInput.files[0];
    const buildCommand = buildCommandInput.value;

    if (!file || !buildCommand) {
        alert('모든 입력 필드를 채워주세요.');
        return;
    }

    const formData = new FormData();
    formData.append('file', file);
    formData.append('buildCommand', buildCommand);

    fetch('/upload', {
        method: 'POST',
        body: formData
    })
    .then(response =&gt; response.text())
    .then(data =&gt; alert('업로드 성공: ' + data))
    .catch(error =&gt; alert('업로드 실패: ' + error.message));
}</code></pre>
<p>이 코드로 사용자는 파일과 명령어를 함께 업로드할 수 있습니다.</p>
<hr />
<h4 id="2-spring-boot에서-파일-업로드-처리">2. <strong>Spring Boot에서 파일 업로드 처리</strong></h4>
<p>서버에서는 Spring Boot를 사용하여 클라이언트로부터 전달된 파일을 저장하고, 추가 데이터를 처리하도록 구현했습니다. 이 과정에서 <code>MultipartFile</code> 객체를 사용하여 파일을 받아 저장했습니다.</p>
<p>다음은 Spring Boot 컨트롤러 코드입니다:</p>
<pre><code class="language-java">@PostMapping
public ResponseEntity&lt;String&gt; handleFileUpload(
        @RequestParam(&quot;file&quot;) MultipartFile file,
        @RequestParam(&quot;buildCommand&quot;) String buildCommand) {
    try {
        String uploadDir = &quot;uploads/&quot;;
        File directory = new File(uploadDir);
        if (!directory.exists()) {
            directory.mkdirs(); // 디렉토리 생성
        }

        String filePath = uploadDir + file.getOriginalFilename();
        file.transferTo(new File(filePath));

        System.out.println(&quot;Received build command: &quot; + buildCommand);

        return ResponseEntity.ok(&quot;파일 업로드 성공: &quot; + file.getOriginalFilename());
    } catch (IOException e) {
        e.printStackTrace();
        return ResponseEntity.status(500).body(&quot;파일 업로드 실패&quot;);
    }
}</code></pre>
<p>이 컨트롤러는 파일을 저장한 뒤 성공 또는 실패 메시지를 반환합니다.</p>
<hr />
<h4 id="3-resources-디렉토리-내-파일-저장-문제">3. <strong>resources 디렉토리 내 파일 저장 문제</strong></h4>
<p>기본적으로 <code>uploads/</code> 디렉토리에 파일을 저장하는 설정은 잘 작동하지만, &quot;파일을 <code>resources</code> 디렉토리 내부에 저장하고 싶다&quot;는 요구가 생겼습니다. </p>
<p>그러나 <code>resources</code> 디렉토리는 Spring Boot에서 읽기 전용으로 설계되었기 때문에, 파일을 저장하려면 다른 접근 방식이 필요했습니다. 아래는 이를 해결한 코드입니다:</p>
<pre><code class="language-java">String resourcePath = new File(&quot;src/main/resources/uploads&quot;).getAbsolutePath();
File directory = new File(resourcePath);

if (!directory.exists()) {
    directory.mkdirs();
}

String filePath = resourcePath + File.separator + file.getOriginalFilename();
file.transferTo(new File(filePath));</code></pre>
<p>이 코드로 파일은 <code>src/main/resources/uploads</code> 폴더에 저장됩니다. 하지만 Spring Boot 실행 중에는 해당 파일에 접근하려면 애플리케이션을 재시작해야 합니다.</p>
<hr />
<h4 id="4-정적-파일-관리와-spring의-기본-동작">4. <strong>정적 파일 관리와 Spring의 기본 동작</strong></h4>
<p>Spring Boot에서 정적 리소스는 <code>src/main/resources/static</code> 디렉토리에 배치하며, 브라우저를 통해 직접 접근할 수 있습니다. 예를 들어, <code>static/index.html</code> 파일은 <code>http://localhost:8080/index.html</code>로 접근할 수 있습니다.</p>
<p>또한, 루트 URL(<code>/</code>)로 접근하면 <code>index.html</code>이 자동으로 표시됩니다. 하지만 동적으로 저장된 파일은 정적 리소스로 제공되지 않기 때문에, 파일이 변경될 때마다 서버를 재시작하거나 외부 디렉토리를 사용하는 것이 효율적입니다.</p>
<hr />
<h4 id="5-학습-포인트">5. <strong>학습 포인트</strong></h4>
<ul>
<li>Spring Boot에서 파일 업로드는 간단하게 구현 가능하지만, 저장 위치와 설정에 따라 다르게 동작합니다.</li>
<li><code>resources</code> 디렉토리 내 파일 저장은 특별한 경우를 제외하고 권장되지 않습니다.</li>
<li>외부 디렉토리를 사용하는 방식이 더 유연하고 안정적입니다.</li>
<li>정적 리소스는 <code>resources/static</code>에 두는 것이 가장 적합합니다.</li>
</ul>
<hr />
<h4 id="6-결론">6. <strong>결론</strong></h4>
<p>오늘은 Spring Boot의 파일 업로드 및 정적 리소스 관리에 대해 깊이 배우는 하루였습니다. 동적으로 파일을 처리해야 하는 경우와 정적으로 파일을 제공해야 하는 경우에 따라 구현 방식이 다르다는 점을 이해하게 되었습니다.</p>
<p>이를 통해, 파일 업로드와 같은 기본 기능을 구현하면서도 다양한 상황에 적응하는 방법을 배울 수 있었습니다. 앞으로 실제 프로젝트에서도 이를 잘 활용할 수 있을 것 같습니다!</p>