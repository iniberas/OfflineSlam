FROM ros:melodic-ros-base

ENV DEBIAN_FRONTEND=noninteractive

# --- System deps ---
# Menggunakan dependencies yang serupa dengan VINS-Fusion, tanpa Ceres
RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates \
    build-essential cmake git wget curl \
    libeigen3-dev libboost-all-dev \
    ros-melodic-cv-bridge ros-melodic-image-transport \
    ros-melodic-tf ros-melodic-rviz \
    python-catkin-tools \
    libopencv-dev \
    && update-ca-certificates \
    && rm -rf /var/lib/apt/lists/*

# --- Catkin workspace ---
ENV CATKIN_WS=/root/catkin_ws
RUN mkdir -p $CATKIN_WS/src
WORKDIR $CATKIN_WS/src

# Clone OpenVINS source
RUN git clone https://github.com/rpng/open_vins.git

# Build menggunakan catkin build (direkomendasikan untuk OpenVINS)
WORKDIR $CATKIN_WS
RUN /bin/bash -c "source /opt/ros/melodic/setup.bash && catkin build"

# Auto-source workspace on container shell start
RUN echo "source /opt/ros/melodic/setup.bash" >> /root/.bashrc && \
    echo "source $CATKIN_WS/devel/setup.bash" >> /root/.bashrc

WORKDIR $CATKIN_WS
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
CMD ["bash"]