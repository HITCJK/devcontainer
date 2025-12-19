FROM osrf/ros:noetic-desktop-full

ARG USERNAME=rosdev
ARG UID=1000
ARG GID=$UID
ARG WORKSPACEFOLDER=ros_ws

# Install some dependencies packages
RUN apt update -q \
    && apt upgrade -q -y \
    && apt install -y --no-install-recommends \
    python3-pip \
    clangd \
    usbutils \
    git
RUN pip install rosdep

# Install YDLidar-SDK
RUN apt install -y python swig
RUN git clone https://github.com/YDLIDAR/YDLidar-SDK.git
RUN mkdir -p YDLidar-SDK/build
RUN cd YDLidar-SDK/build \
    && cmake .. \
    && make \
    && sudo make install
RUN cd .. && cd .. && rm -rf YDLidar-SDK

# Install realsense2_camera
RUN apt install -y ros-noetic-realsense2-camera

# Install dashgo's dependencies
COPY ./src/dashgo ./src/dashgo
RUN rosdep update && rosdep install --from-paths src/dashgo --ignore-src -r -y
RUN apt install -y ros-noetic-serial ros-noetic-ecl-threads ros-noetic-robot-pose-ekf ros-noetic-rgbd-launch libudev-dev udev
RUN pip install pyserial
RUN rm -rf /home/$USERNAME/$WORKSPACEFOLDER/src/dashgo

# Create and switch to user
RUN groupadd -g $GID $USERNAME \
    && useradd -lm -u $UID -g $USERNAME -s /bin/bash $USERNAME \
    && echo "$USERNAME ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
RUN adduser $USERNAME video
RUN adduser $USERNAME dialout

# different groups for arch
RUN groupadd -g 984 archvedio \
    && groupadd -g 985 archuucp \
    && adduser $USERNAME archvedio \
    && adduser $USERNAME archuucp

USER $USERNAME

# Create workspace so that user own this directory
RUN mkdir -p /home/$USERNAME/$WORKSPACEFOLDER
WORKDIR /home/$USERNAME/$WORKSPACEFOLDER

# Copy configuration files
RUN echo 'source /opt/ros/$ROS_DISTRO/setup.bash' >> /home/$USERNAME/.bashrc \
    && echo 'source /home/'$USERNAME'/'$WORKSPACEFOLDER'/devel/setup.bash' >> /home/$USERNAME/.bashrc