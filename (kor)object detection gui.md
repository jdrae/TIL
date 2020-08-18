<h1 id="kor--object-detection-gui">(kor)  Object Detection GUI</h1>
<p>실시간으로 웹캠에 주어지는 이미지를 받고, 물체를 감지하여 클래스 별로 분류하는 GUI 프로그램을 만든다.</p>
<h2 id="gui-디자인">GUI 디자인</h2>
<p><img src="https://user-images.githubusercontent.com/68010286/90461658-e176bb00-e141-11ea-8878-4308b37327ca.png" alt="그림1"></p>
<ul>
<li>웹캠은 640x480 의 고정된 사이즈로 받는다. (1280x720 웹캠 테스트 결과 에러 없음)</li>
<li>start 버튼을 클릭하지 않아도 디폴트로 웹캠이 연결된다. model 과 weight 파일을 select 하고, start 버튼을 클릭하면 detection 결과가 표시된 화면이 출력된다.</li>
<li>model 과 weight 파일을 불러오는데 에러가 생기면 에러메세지를 알람으로 띄우고, 화면은 기본 웹캠 입력으로 바뀐다. 특정 확장명(.h5 와 .weights)만을 읽을 수 있다.</li>
<li>파일의 경로가 너무 길 경우, 파일명이 표시될 수 있도록 중간 문자열을 생략한다.</li>
<li>record 버튼을 클릭하면, 화면이 녹화되며 버튼의 값은 stop 으로 바뀐다. stop 버튼을 클릭하면 비디오가 저장되며, 값은 다시 record 로 바뀐다.</li>
</ul>
<h2 id="프로그램-설계">프로그램 설계</h2>
<ol>
<li><code>device.py</code><br>
사용할 웹캠을 지정하고, 프레임을 읽어온다.</li>
</ol>
<ul>
<li>q 키를 눌러야 창이 닫힌다.</li>
</ul>
<ol start="2">
<li><code>gui.py</code><br>
위의 gui 를 조합하고, 웹캠에서 받아온 프레임을 표시한다.</li>
</ol>
<ul>
<li>timer를 사용해 표시할 프레임을 업데이트 한다.</li>
</ul>
<ol start="3">
<li>
<p><code>detect.py</code><br>
yolo 를 활용하여 프레임에서 애호박의 위치를 찾는다.</p>
</li>
<li>
<p><code>classify.py</code><br>
애호박의 위치를 전달받아, 학습된 모델로 애호박의 클래스를 분류한다.  <em>개발중</em></p>
</li>
<li>
<p><code>main.py</code><br>
GPU 의 사용 제한을 풀고, 통합된 프로그램을 실행한다.</p>
</li>
</ol>
<h2 id="문제점-속도와-gpu-메모리">문제점: 속도와 GPU 메모리</h2>
<p>단순히 웹캠 이미지를 띄울 때는 속도나 메모리 문제가 없다. 하지만 yolov3.weights 파일을 적용했을 경우, 감지되는 물체가 많아질수록 GPU 메모리가 초과되어 실행이 중단된다. 한편 직접 만든 weights 파일을 사용할 경우, 메모리 문제는 없으나 프레임을 보내는 속도가 매우 느려진다.</p>
<p>해결방안으로 가능성이 있는 후보는 다음과 같다.</p>
<h3 id="darkflow-또는-darknet">1. <a href="https://github.com/thtrieu/darkflow">darkflow</a> 또는 darknet</h3>
<p>darknet 이 더 빠르다고 한다. <a href="https://m.blog.naver.com/estern/221593424910">출처</a></p>
<p>darkflow 는 yolov2까지만 지원하여, yolov3 모델은 적용이 불가능하다. yolov3 의 확장자를 바꾸는 방법이 있다고 하는데 (<a href="https://github.com/thtrieu/darkflow/issues/964">출처</a>) 자세한 건 알아봐야한다.</p>
<h3 id="threading">2. threading</h3>
<p>스택 오버플로우에서 gpu 메모리는 threading 으로 해결되지 않는다는 글을 언뜻봤다. <em>출처 필요</em></p>
<h3 id="multiprocessing">3. multiprocessing</h3>
<p><a href="https://github.com/pythonlessons/YOLOv3-object-detection-tutorial">YOLOv3-object-detection-tutorial</a></p>
<p><code>realtime_detect.py</code> 에서 multiprocessing 모듈을 사용한다.</p>
<h3 id="yolo-파일을-tensorflow-파일로-바꾸기">4. yolo 파일을 tensorflow 파일로 바꾸기</h3>
<p><a href="https://github.com/jinyu121/DW2TF">DW2TF: Darknet to TensorFlow</a></p>
<ul>
<li>Darknet weights (<code>.weights</code>) to TensorFlow weights (<code>.ckpt</code>)</li>
<li>Darknet model (<code>.cfg</code>) to TensorFlow graph (<code>.pb</code>,  <code>.meta</code>)</li>
</ul>
<p>yolo 파일을 불러오는 방식에서 문제가 있는 것일 수도 있기 때문에, 다른 확장자로도 테스트를 해봐야겠다.</p>
<ul>
<li>weights 파일을 h5 로도 바꿀 수 있다.<a href="https://pylessons.com/YOLOv3-WebCam/">(참고)</a></li>
</ul>
<p>그런데 변환하면 성능이 낮아진다는 얘기가 있다. <a href="https://eehoeskrap.tistory.com/352">출처</a></p>
<h3 id="영상-프레임-자체를-조절하기">5. 영상 프레임 자체를 조절하기</h3>
<p>속도가 늦어지고 GPU 메모리를 많이 잡아먹는 근본적인 이유는 FPS 가 높은 데에 비해 물체를 detect 하는 과정은 프레임 당 시간이 오래 걸리기 때문이다.</p>
<p>따라서 다음과 같은 두 가지 방법을 생각해 볼 수 있다.</p>
<ol>
<li>영상 창을 두개를 띄우고, 한 쪽은 기본 웹캠을 보여주고, 다른 쪽은 프레임을 queue 에 받아 detect 처리 한 결과를 시간차를 두고(detect 되는 즉시) 내보낸다.</li>
<li>또는 받아오는 모든 프레임을 detect 하지 않고, 일정 수마다(ex) fps 가 15라면 초당 3개 정도의 프레임) detect 를 하여 확인된 위치를 받아 다음 프레임에 표시한다.</li>
</ol>
<h2 id="해볼-것">해볼 것</h2>
<ul>
<li>2020-08-18 작성
<ul>
<li>darknet 사용하기 (darkflow 시도했었음)</li>
<li>multiprocessing 모듈</li>
<li>threading 더 공부하기 - 시도했으나 효과가 없다는 글을 보고 잠시 미룸</li>
<li>yolo 파일은 미루고 h5 파일 적용해보기</li>
<li>gui 와 알고리즘 연결: 모델 파일 선택 후 즉각적으로 웹캠에 반영</li>
</ul>
</li>
</ul>
<h2 id="진행사항">진행사항</h2>
<ul>
<li>2020-08-18 작성
<ul>
<li>속도 향상 자료 조사</li>
<li>gui 버튼 추가</li>
<li>gui 에서 파일 경로를 변수에 저장</li>
</ul>
</li>
</ul>
<p><img src="https://user-images.githubusercontent.com/45510328/90504178-a4371b00-e18b-11ea-908a-af5b9ce8c77e.png" alt="0818-gui"></p>
<h2 id="참고문헌">참고문헌</h2>
<p><a href="https://github.com/thtrieu/darkflow">darkflow</a></p>
<p><a href="https://reyrei.tistory.com/16">conda 가상환경에 darkflow 설치</a><br>
<code>python3 setup.py build_ext --inplace</code> 가 안되면 <code>python setup.py build_ext --inplace</code>로 시도.</p>
<p><a href="https://junyoung-jamong.github.io/deep/learning/2019/01/22/Darkflow%EB%A5%BC-%ED%99%9C%EC%9A%A9%ED%95%B4-YOLO%EB%AA%A8%EB%8D%B8-%EC%9D%B4%EB%AF%B8%EC%A7%80-%EB%94%94%ED%85%8D%EC%85%98-%EA%B5%AC%ED%98%84-in-windows.html">Darkflow를 활용하여 YOLO 모델로 이미지 디텍션 구현(윈도우 환경)</a></p>
<p><a href="https://github.com/AlbertSuarez/yolo-webcam-object-detection">YOLO Webcam Object detection</a><br>
darkflow 사용<br>
TensorFlow 1.14<br>
yolov2 사용 (yolov3을 지원하지 않는다)</p>
<p><a href="https://github.com/pythonlessons/YOLOv3-object-detection-tutorial">YOLOv3-object-detection-tutorial</a><br>
TensorFlow 1.15<br>
Keras 2.2.4<br>
multiprocessing 사용</p>
<p><a href="https://github.com/pythonlessons/TensorFlow-2.x-YOLOv3">TensorFlow-2.x-YOLOv3</a></p>
<p><a href="https://mickael-k.tistory.com/15">Darknet 설치</a></p>

