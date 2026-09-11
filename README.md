## 1. Allow GUI apps (rviz / Pangolin viewer) from Docker

On your host, once per session:
```bash
xhost +local:docker
```

## 2. Build

```bash
docker compose build
```
This will take a while the first time (compiling OpenCV, Ceres, Pangolin, etc).

## 3. Run VINS-Fusion

```bash
docker compose run --rm vins-fusion bash
roslaunch vins vins_rviz.launch
rosrun vins vins_node /root/data/camera_calibration.yaml
rosrun global_fusion global_fusion_node /gps:=/mavros/global_position/global
rosbag play /root/data/rosbag_bener.bag
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