---


---

<h1 id="px4-docker-containers">PX4 Docker Containers</h1>
<p>Docker container는 Gazebo와 ROS 시뮬레이션을 포함한 전체 PX4 개발 툴체인을 가지고 있습니다.:</p>
<ul>
<li><strong>px4io/px4-dev</strong>: 시뮬레이션을 포함하는 툴체인</li>
<li><strong>px4io/px4-dev-ros</strong>: 시뮬레이션과 ROS(MAVROS)를 포함하는 툴체인</li>
</ul>
<p>제대로 동작하는 container를 원하는 경우 <code>px4io/px4-dev-ros:v1.0</code>와 같이 태그가 달린 이미지 중에 하나를 선택합니다. <code>최신</code> container의 경우 변경 내용이 많습니다.</p>
<p>Dockerfiles와 README는 여기에 : <a href="https://github.com/PX4/containers/tree/master/docker/px4-dev">https://github.com/PX4/containers/tree/master/docker/px4-dev</a></p>
<p>Docker Hub에서 자동으로 생성 : <a href="https://hub.docker.com/u/px4io/">https://hub.docker.com/u/px4io/</a></p>
<h2 id="전제-조건">전제 조건</h2>
<p><a href="https://docs.docker.com/installation/">https://docs.docker.com/installation/</a> 에서 Docker를 설치하고, 최신 버전은 Docker-maintained package 저장소 중에 하나를 사용하는 것이 좋습니다.</p>
<p>container는 현재 Linux만 지원합니다. Linux가 없다면 가상머신을 이용해서 container를 실행할 수 있습니다. 아래에 좀더 상세하게 설명하겠습니다. 기본 Linux 이미지의 <code>boot2docker</code>를 사용하지 마세요. 왜냐하면 X-Server가 포함되어 있지 않기 때문입니다.</p>
<h2 id="docker-container-사용하기">Docker container 사용하기</h2>
<p>이번에는 X forwarding를 지원하는 Docker container를 실행합니다. X forwarding로 container 내부에서 시뮬레이션 GUI 사용이 가능합니다. 또 여러분 컴퓨터의 디렉토리 <code>&lt;local_src&gt;</code>를 container 내부의 <code>&lt;container_src&gt;</code>로 매핑하고, QGC 연결에 필요한 UDP port를 forward합니다. 네트워크 포트 매핑 관련해서 좀더 자세한 정보를 원한다면 Docker 문서를 참고하세요.</p>
<p><code>-–privileged</code> 옵션을 사용하면 자동으로 여러분의 호스트의 장치(조이스틱이나 GPU)에 접근이 가능합니다. 장치를 연결/해제하고자 한다면 container를 재시작시켜야만 합니다.</p>
<pre><code># enable access to xhost from the container
xhost +

docker run -it --privileged \
    -v &lt;local_src&gt;:&lt;container_src&gt;:rw \
    -v /tmp/.X11-unix:/tmp/.X11-unix:ro \
    -e DISPLAY=:0 \
    -p 14556:14556/udp \
    --name=container_name px4io/px4-dev bash

</code></pre>
<p>여기까지 정상적으로 진행되었다면 새 bash 쉘 상태가 됩니다. SITL를 실행시켜서 정상적으로 동작하는지를 검증할 수 있습니다. :</p>
<pre><code>cd &lt;container_src&gt;
make posix_sitl_default gazebo

</code></pre>
<h3 id="그래픽-드라이버-문제">그래픽 드라이버 문제</h3>
<p>Gazebo 실행시 다음과 같은 에러 메시지가 발생할 수 있습니다. :</p>
<pre><code>libGL error: failed to load driver: swrast

</code></pre>
<p>이런 경우 호스트 시스템의 그래픽 드라이버를 설치해야만 합니다. 적절한 드라이버를 다운로드하고 container 내부에서 설치합니다. Nvidia 드라이버인 경우 다음과 같은 명령을 사용해야 합니다. (그렇지 않아면 인스톨러는 호스트에서 로드된 모듈을 보고 진행을 취소시킬 수 있음) :</p>
<pre><code>./NVIDIA-DRIVER.run -a -N --ui=none --no-kernel-module

</code></pre>
<p>관련 정보는 여기서 : <a href="http://gernotklingler.com/blog/howto-get-hardware-accelerated-opengl-support-docker/">http://gernotklingler.com/blog/howto-get-hardware-accelerated-opengl-support-docker/</a></p>
<h3 id="re-enter-the-container">Re-enter the container</h3>
<p>container를 종료하면, 변경 내용이 container에 남게된다. 위에 “docker run” 명령은 새로운 container를 생성하는데만 사용할 수 있습니다. 이 container로 돌아가려면 간단하게 아래와 같이 하면 됩니다. :</p>
<pre><code># start the container
sudo docker start container_name
# open a new bash shell in this container
sudo docker exec -it container_name bash

</code></pre>
<p>이 container에 연결하는 여러 shell이 필요하다면, 새로운 shell을 열고 마지막 명령을 다시 실행하세요.</p>
<h2 id="가상머신-지원">가상머신 지원</h2>
<p>최근 Linux 배포판이 정상적으로 동작해야합니다.</p>
<p>테스트한 설정 :</p>
<ul>
<li>OS X에서 VMWare Fusion과 Ubuntu 14.04 (Parallels에서 GUI 지원하는 Docker container의 경우 X-Server에 문제 발생시킴)</li>
</ul>
<p><strong>메모리</strong></p>
<p>가상머신에 최소한 4GB 메모리를 사용합니다.</p>
<p><strong>컴파일 문제</strong></p>
<p>다음과 같이 에러로 컴파일이 실패하면 :</p>
<pre><code>The bug is not reproducible, so it is likely a hardware or OS problem.
c++: internal compiler error: Killed (program cc1plus)

</code></pre>
<p>병렬 빌드를 비활성화 시킵니다.</p>
<p><strong>VM Host에서 Docker Control 허용</strong></p>
<p><code>/etc/defaults/docker</code> 수정하고 다음 한줄을 추가합니다. :</p>
<pre><code>DOCKER_OPTS="${DOCKER_OPTS} -H unix:///var/run/docker.sock -H 0.0.0.0:2375"

</code></pre>
<p>다음으로 여러분의 host OS에서 docker를 제어할 수 있습니다. :</p>
<pre><code>export DOCKER_HOST=tcp://&lt;ip of your VM&gt;:2375
# run some docker command to see if it works, e.g. ps
docker ps

</code></pre>
<h2 id="legacy">Legacy</h2>
<p>ROS 멀티플랫폼 container는 더이상 유지보수하지 않습니다. : <a href="https://github.com/PX4/containers/tree/master/docker/ros-indigo">https://github.com/PX4/containers/tree/master/docker/ros-indigo</a></p>

