아래 내용을 그대로 복사해서 `README.md` 파일에 붙여넣으시면 됩니다. 요청하신 대로 본래 말투와 내용은 유지하면서 가독성만 높였습니다.

---



# camera_lidar-fusion

이 레포지토리는 카메라랑 라이다를 퓨전하는 레포지토리이다.

센서퓨전을 하기 위해서는 다음과 같은 값들 필요하다. 먼저 카메라와 lidar 사이의 extrinsic matrix를 구해야 한다. 나는 그 extrinsic matrix를 구하는 방법을 matlab을 이용해서 구할 것이다.



---

## 1. 데이터 수집 (Data Collection)

`get_extrinsic_matrix.m`을 사용하면 lidar와 카메라 사이의 관계를 구할 수 있다. 근데 그러기 위해서는 각각 동일한 시간에 해당하는 `.pointcloud2` 파일이랑 이미지가 필요하다. 그걸 하기 위해서 가장 먼저 해야 하는 일은 아래 명령어를 실행하는 것이다.

```bash
ros2 run camera_to_png_lidar_to_pcd_for_matlab calib_data_saver

```

### 파라미터 정보

내부에 파라미터를 보면 다음과 같은데 굳이 변경하지 않는 걸 추천한다.

* `time_interval` (10.0): 저장 간격 (초). 10.0초 라는건 한번 찍히면 다음 촬영까지 대기가 10초라는거다. 그 사이에 빨리 새로운 pose를 잡자.
* `sync_slop` (0.1): 시점 허용 차이
* `camera_only` (false): 모드 선택. false는 그대로 두면 된다. true를 해버리면 camera만 찍힌다. 우린 라이다랑 카메라랑 같이 찍어야 하니까 false이다.
* `target_count` (20): 목표 저장 개수

위 명령을 이용해 촬영을 모두 마치게 되면 data들이 **calibration_data**라는 폴더에 사진은 `/images`에 pointcloud는 `/lidar`에 저장되게 된다.

---



## 2. 포인트클라우드 전처리 (ROI 설정)

이제 촬영이 완료가 되었으면 바로 추출을 하면 좋겠지만 우리 matlab은 거기서 잘 못 뽑아낸다. 그래서 우리가 추가적인 작업을 해야한다. 바로 그 파일은 `extract_plane_from_pointcloud_by_step.m` 이 파일이다.

### 작업 내용

우리가 저장한 그 pointcloud2(.pcd파일) 에서 체커보드에 해당하는걸 골라내면 된다. 어렵지 않다 딸깍딸깍이다. 근데 matlab 보고 바로 체커보드 추출하라 하면 추출을 못한다. 그래서 우리가 미리 존재하는 구역만 남겨두고 다 버리고 거기서 찾게 한다.

```matlab
% roi = [0.0 1.6 -0.8 0.6 -0.25 0.8];
% indicies = findPointsInROI(ptCloud, roi);
% ptCloud2 = select(ptCloud, indicies);
% figure
% pcshow(ptCloud2);

```

이게 그 부분이다. **roi에 앞에서부터 x축 최소, x축 최대, y축 최소, y축 최대...** 이런식으로 나간다. 저 roi 부분만 남겨두고 나머지는 다 삭제해 버린다. 그래서 그냥 한마디로 roi안에 우리 체커보드만 남기는 구역 설정만 잘 하면 된다. (x,y,z는 lidar 기준이니까 잘 할 수 있을꺼라 믿는다.)

### 주의사항 및 팁

* **boardSize**의 경우 이건 판의 크기를 의미하는거니까 똑같은 판 사용하면 고정이다.
* 쭉쭉 진행하면 잘 나온다. 그리고 이건 저장을 하니까 매번 RUN이라는 버튼을 누를때마다 3가지를 바꿔줘야 한다.
* `calibration_data/lidar/012.pcd");` 코드를 보면 이 부분이 총 3개 있는데 처음에 001.pcd에서 진행했으면 다음에는 002.pcd로 저 3부분을 그냥 바꿔주면 된다.

그리고 마지막에 평면이 잘 보이면 **(다음 사진)**과 같이 저장이 잘 된거니까 마지막 부분으로 가면 된다.

**(사진 넣을 부분)**

---



## 3. Extrinsic Matrix 산출

그리고 이제 마지막으로 가면 되는 부분이 `get_extrinsic_matrix.m`이다.
파일 경로는 알아서 공부해서 바꿔봅시다!!! 나중에 진짜 유용합니다!!!

**(사진 넣을 부분)**

---

**이 외에 더 추가하고 싶은 내용(설치 방법이나 환경 세팅 등)이 있으시면 언제든 말씀해주세요!**


