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
    usbutils
RUN pip install rosdep

# Create and switch to user
RUN groupadd -g $GID $USERNAME \
    && useradd -lm -u $UID -g $USERNAME -s /bin/bash $USERNAME \
    && echo "$USERNAME ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
RUN adduser $USERNAME video
USER $USERNAME


# Create workspace so that user own this directory
RUN mkdir -p /home/$USERNAME/$WORKSPACEFOLDER
WORKDIR /home/$USERNAME/$WORKSPACEFOLDER

# Copy configuration files
RUN echo 'source /opt/ros/$ROS_DISTRO/setup.bash' >> /home/$USERNAME/.bashrc \
    && echo 'source /home/'$USERNAME'/'$WORKSPACEFOLDER'/devel/setup.bash' >> /home/$USERNAME/.bashrc