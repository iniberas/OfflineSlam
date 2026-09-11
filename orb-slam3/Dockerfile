FROM ubuntu:20.04

ENV DEBIAN_FRONTEND=noninteractive

# --- ROS Noetic repo + install ---
RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates curl gnupg2 lsb-release \
    && update-ca-certificates \
    && curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | apt-key add - \
    && echo "deb http://packages.ros.org/ros/ubuntu focal main" > /etc/apt/sources.list.d/ros-latest.list \
    && apt-get update && apt-get install -y --no-install-recommends \
    ros-noetic-ros-base \
    python3-rosdep python3-catkin-tools python3-osrf-pycommon \
    ros-noetic-cv-bridge ros-noetic-image-transport \
    && rosdep init && rosdep update \
    && rm -rf /var/lib/apt/lists/*

# --- System deps buat ORB-SLAM3 ---
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential cmake git wget curl unzip pkg-config \
    libeigen3-dev libglew-dev libboost-all-dev \
    libssl-dev libgtk2.0-dev libavcodec-dev libavformat-dev \
    libswscale-dev libtbb2 libtbb-dev libjpeg-dev libpng-dev \
    libtiff-dev libopenexr-dev python3-dev python3-numpy \
    libxkbcommon-x11-0 libpython3-dev \
    && rm -rf /var/lib/apt/lists/*

# --- OpenCV 4.4 (dipakai konsisten di core DAN wrapper ROS) ---
WORKDIR /opt
RUN git clone --depth 1 --branch 4.4.0 https://github.com/opencv/opencv.git && \
    git clone --depth 1 --branch 4.4.0 https://github.com/opencv/opencv_contrib.git && \
    mkdir opencv/build && cd opencv/build && \
    cmake -D CMAKE_BUILD_TYPE=Release \
          -D CMAKE_INSTALL_PREFIX=/usr/local \
          -D OPENCV_EXTRA_MODULES_PATH=/opt/opencv_contrib/modules \
          -D BUILD_EXAMPLES=OFF -D BUILD_TESTS=OFF -D BUILD_PERF_TESTS=OFF \
          .. && \
    make -j$(nproc) && make install && ldconfig && \
    cd /opt && rm -rf opencv opencv_contrib

# --- Pangolin (visualization) ---
WORKDIR /opt
RUN git clone --recursive https://github.com/stevenlovegrove/Pangolin.git && \
    cd Pangolin && git checkout v0.6 && \
    mkdir build && cd build && \
    cmake .. -DCMAKE_BUILD_TYPE=Release && \
    make -j$(nproc) && make install && ldconfig && \
    cd /opt && rm -rf Pangolin

# --- Catkin workspace ---
ENV CATKIN_WS=/root/catkin_ws
RUN mkdir -p $CATKIN_WS/src
WORKDIR $CATKIN_WS/src

# ORB-SLAM3 core (dari repo resmi, dipakai buat library-nya)
RUN git clone https://github.com/UZ-SLAMLab/ORB_SLAM3.git ORB_SLAM3_core
WORKDIR $CATKIN_WS/src/ORB_SLAM3_core
RUN chmod +x build.sh && ./build.sh

# --- ROS wrapper: pakai fork thien94, catkin-native, sudah fix OpenCV4 ---
# CATATAN: struktur exact repo ini perlu dicek README-nya begitu ke-clone,
# ada kemungkinan perlu adjust path/CMakeLists menyesuaikan versi ORB_SLAM3_core
# di atas. Kalau ada error build spesifik di sini, itu next thing to fix.
WORKDIR $CATKIN_WS/src
RUN git clone https://github.com/thien94/orb_slam3_ros.git

WORKDIR $CATKIN_WS
RUN /bin/bash -c "source /opt/ros/noetic/setup.bash && catkin_make -j$(nproc)"

RUN echo "source /opt/ros/noetic/setup.bash" >> /root/.bashrc && \
    echo "source $CATKIN_WS/devel/setup.bash" >> /root/.bashrc

WORKDIR $CATKIN_WS
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
CMD ["bash"]