FROM nvidia/cuda:12.6.2-cudnn-devel-ubuntu22.04

ENV DEBIAN_FRONTEND=noninteractive

# Base dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    wget \
    sudo \
    git \
    curl \
    ca-certificates \
    software-properties-common \
    ninja-build \
    libopencv-dev \
    python3-opencv \
    python3-pip \
    python3-dev \
    python3-venv \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Use Python 3.10
RUN ln -sf /usr/bin/python3 /usr/bin/python && \
    python -m pip install --upgrade pip

# Create non-root user
ARG USER_ID=1000
RUN useradd -m --no-log-init --system --uid ${USER_ID} appuser -g sudo && \
    echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

USER appuser
WORKDIR /home/appuser

ENV PATH="/home/appuser/.local/bin:${PATH}"

# Install PyTorch + dependencies
RUN pip install --user torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu121 && \
    pip install --user tensorboard cmake onnx 'git+https://github.com/facebookresearch/fvcore'

# Install Detectron2 from GitHub (not your local copy)
RUN git clone https://github.com/facebookresearch/detectron2 /home/appuser/detectron2 && \
    pip install --user -e /home/appuser/detectron2

# Environment variables
ENV FORCE_CUDA="1"
ARG TORCH_CUDA_ARCH_LIST="Volta;Turing;Ampere;Ada"
ENV TORCH_CUDA_ARCH_LIST="${TORCH_CUDA_ARCH_LIST}"
ENV FVCORE_CACHE="/tmp"

WORKDIR /workspace