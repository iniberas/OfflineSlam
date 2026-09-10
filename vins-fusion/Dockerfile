FROM ros:melodic-ros-base

ENV DEBIAN_FRONTEND=noninteractive

# --- System deps ---
RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates \
    build-essential cmake git wget curl \
    libeigen3-dev libboost-all-dev \
    libgoogle-glog-dev libgflags-dev libatlas-base-dev \
    libsuitesparse-dev \
    ros-melodic-cv-bridge ros-melodic-image-transport \
    ros-melodic-tf ros-melodic-rviz ros-melodic-pcl-ros \
    python-catkin-tools \
    libopencv-dev \
    && update-ca-certificates \
    && rm -rf /var/lib/apt/lists/*

# --- Ceres Solver 1.14.0
WORKDIR /opt
RUN git clone --depth 1 --branch 1.14.0 https://github.com/ceres-solver/ceres-solver.git && \
    cd ceres-solver && mkdir build && cd build && \
    cmake .. -DCMAKE_BUILD_TYPE=Release && \
    make -j$(nproc) && make install && \
    cd /opt && rm -rf ceres-solver

# --- Catkin workspace ---
ENV CATKIN_WS=/root/catkin_ws
RUN mkdir -p $CATKIN_WS/src
WORKDIR $CATKIN_WS/src

# VINS-Fusion source
RUN git clone https://github.com/HKUST-Aerial-Robotics/VINS-Fusion.git

WORKDIR $CATKIN_WS
RUN /bin/bash -c "source /opt/ros/melodic/setup.bash && catkin_make -j$(nproc)"

# Auto-source workspace on container shell start
RUN echo "source /opt/ros/melodic/setup.bash" >> /root/.bashrc && \
    echo "source $CATKIN_WS/devel/setup.bash" >> /root/.bashrc

WORKDIR $CATKIN_WS
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
CMD ["bash"]