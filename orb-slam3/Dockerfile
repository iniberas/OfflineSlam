FROM ubuntu:20.04

ENV DEBIAN_FRONTEND=noninteractive

# --- System deps ---
RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates \
    build-essential cmake git wget curl unzip pkg-config \
    libeigen3-dev libglew-dev libboost-all-dev \
    libssl-dev libgtk2.0-dev libavcodec-dev libavformat-dev \
    libswscale-dev libtbb2 libtbb-dev libjpeg-dev libpng-dev \
    libtiff-dev libopenexr-dev python3-dev python3-numpy \
    libxkbcommon-x11-0 libpython3-dev \
    && update-ca-certificates \
    && rm -rf /var/lib/apt/lists/*

# --- OpenCV 4.4 (ORB-SLAM3-friendly version) ---
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

# --- ORB-SLAM3 source ---
WORKDIR /opt
RUN git clone https://github.com/UZ-SLAMLab/ORB_SLAM3.git

WORKDIR /opt/ORB_SLAM3
# build.sh compiles DBoW2, g2o, Sophus (vendored) then ORB-SLAM3 itself
RUN chmod +x build.sh && ./build.sh

WORKDIR /opt/ORB_SLAM3
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
CMD ["bash"]
