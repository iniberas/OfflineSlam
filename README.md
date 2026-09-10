## 1. Allow GUI apps (rviz / Pangolin viewer) from Docker

On your host, once per session:
```bash
xhost +local:docker
```

## 2. Build

```bash
cd slam-docker
docker compose build
```
This will take a while the first time (compiling OpenCV, Ceres, Pangolin, etc).

## 3. Run VINS-Fusion

```bash
docker compose run --rm vins-fusion bash
# inside container:
roslaunch vins vins_rviz.launch
# in a second terminal, attach to same container:
docker exec -it vins_fusion bash
rosrun vins vins_node /root/catkin_ws/src/VINS-Fusion/config/euroc/euroc_stereo_imu_config.yaml
# then play your rosbag (mounted at /root/data) from a third terminal
rosbag play /root/data/MH_01_easy.bag
```

## 4. Run ORB-SLAM3

```bash
docker compose run --rm orb-slam3 bash
# inside container, e.g. EuRoC monocular-inertial example:
./Examples/Monocular-Inertial/mono_inertial_euroc \
    ./Vocabulary/ORBvoc.txt \
    ./Examples/Monocular-Inertial/EuRoC.yaml \
    /root/data/MH_01_easy \
    ./Examples/Monocular-Inertial/EuRoC_TimeStamps/MH01.txt
```