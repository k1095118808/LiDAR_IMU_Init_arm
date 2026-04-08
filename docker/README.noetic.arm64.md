# LI-Init Docker (ROS Noetic, ARM64 / Jetson Orin NX)

A ROS Noetic build of LI-Init for ARM64 platforms such as the NVIDIA Jetson Orin NX.
See `Dockerfile.noetic.arm64`.

## Build

From the repo root:

```bash
docker build -f docker/Dockerfile.noetic.arm64 -t li_init:noetic-arm64 docker/
```

## Run

Allow GUI access from the container:

```bash
xhost +local:docker
```

Launch the container (Jetson uses the `nvidia` runtime, not `nvidia-docker`):

```bash
docker run --privileged -it --runtime nvidia \
    --volume=${PWD}:/home/catkin_ws/src/LiDAR_IMU_Init \
    --volume=/tmp/.X11-unix:/tmp/.X11-unix:rw \
    --net=host --ipc=host --shm-size=1gb \
    --env="DISPLAY=$DISPLAY" \
    --name=li_init \
    li_init:noetic-arm64 /bin/bash
```

On success you should see:

```
================Docker Env Ready================
root@jetson:/home/catkin_ws#
```

## Build & launch LI-Init

Inside the container:

```bash
cd /home/catkin_ws
catkin_make
source devel/setup.bash
roslaunch lidar_imu_init xxx.launch
```

Results are written to `LiDAR_IMU_Init/result/Initialization_result.txt`.

## Notes

- Base image: `arm64v8/ros:noetic-ros-base`.
- Builds Ceres 2.0.0 from source and includes `livox_ros_driver` v2.6.0.
- Tested target: Jetson Orin NX (JetPack / L4T, ARM64).
- For x86_64 + Melodic, use the original `Dockerfile` and `docker_start.md`.
