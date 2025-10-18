# Stonefish Marine Robotics Simulator - Development Environment
# Based on ROS Noetic Desktop Full with GPU support

FROM osrf/ros:noetic-desktop-full

# Avoid interactive prompts during build
ENV DEBIAN_FRONTEND=noninteractive

# Install system dependencies
RUN apt-get update && apt-get install -y \
    # Build tools
    git \
    cmake \
    build-essential \
    # Stonefish dependencies
    libglm-dev \
    libsdl2-dev \
    libfreetype6-dev \
    # Additional utilities
    wget \
    curl \
    vim \
    nano \
    && rm -rf /var/lib/apt/lists/*

# Fix SDL2 CMake configuration if needed
RUN if [ -f /usr/lib/x86_64-linux-gnu/cmake/SDL2/sdl2-config.cmake ]; then \
        sed -i 's/-lSDL2 /-lSDL2/g' /usr/lib/x86_64-linux-gnu/cmake/SDL2/sdl2-config.cmake; \
    fi

# Set working directory
WORKDIR /workspace

# GitHub Personal Access Token (passed as build arg)
ARG GITHUB_TOKEN

# Clone and build Stonefish library
RUN git clone https://${GITHUB_TOKEN}@github.com/HERO-Lab-POSTECH/stonefish.git && \
    cd stonefish && \
    rm -rf build && \
    mkdir build && \
    cd build && \
    cmake .. && \
    make -j$(nproc) && \
    make install && \
    ldconfig

# Create ROS workspace
RUN mkdir -p /workspace/catkin_ws/src
WORKDIR /workspace/catkin_ws

# Initialize catkin workspace
RUN /bin/bash -c "source /opt/ros/noetic/setup.bash && catkin_make"

# Set up environment variables
ENV ROS_DISTRO=noetic
ENV CATKIN_WS=/workspace/catkin_ws

# Source ROS and workspace in bashrc
RUN echo "source /opt/ros/noetic/setup.bash" >> /root/.bashrc && \
    echo "source /workspace/catkin_ws/devel/setup.bash" >> /root/.bashrc && \
    echo "export ROS_HOSTNAME=localhost" >> /root/.bashrc && \
    echo "export ROS_MASTER_URI=http://localhost:11311" >> /root/.bashrc

# Set display for GUI applications
ENV DISPLAY=:0
ENV QT_X11_NO_MITSHM=1

# Set working directory to workspace
WORKDIR /workspace

# Default command
CMD ["/bin/bash"]
