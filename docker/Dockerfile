FROM nvidia/cuda:12.1.1-devel-ubuntu20.04
ENV TZ=Asia/Shanghai
ENV DEBIAN_FRONTEND=noninteractive
ENV PATH="/root/miniconda3/bin:${PATH}"
RUN apt-get update && \
    apt-get install -y git git-lfs aria2 ffmpeg libsm6 libxext6 && \
    rm -rf /var/lib/apt/lists/* && \
    aria2c --console-log-level=error -c -x 16 -s 16 -k 1M https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -o Miniconda3-latest-Linux-x86_64.sh && \
    mkdir /root/.conda && \
    bash Miniconda3-latest-Linux-x86_64.sh -b && \
    rm -f Miniconda3-latest-Linux-x86_64.sh
RUN git lfs install
# 下载stable-diffusion-webui本体
RUN git clone -b v2.6 "https://github.com/camenduru/stable-diffusion-webui" && \
    cd "stable-diffusion-webui" && \
    rm -rf .git
WORKDIR stable-diffusion-webui

RUN pip install -q torch==2.0.1+cu118 torchvision==0.15.2+cu118 torchaudio==2.0.2+cu118 --extra-index-url https://download.pytorch.org/whl/cu118 -U --no-cache-dir

# 完成其他依赖的安装
RUN python launch.py --exit --xformers --skip-torch-cuda-test

# 下载常用的 negative prompt
RUN git lfs clone https://huggingface.co/embed/negative embeddings/negative 
# 图库浏览器
RUN git clone https://github.com/zanllp/sd-webui-infinite-image-browsing extensions/sd-webui-infinite-image-browsing
RUN git clone https://github.com/DominikDoom/a1111-sd-webui-tagcomplete extensions/a1111-sd-webui-tagcomplete
RUN git clone https://github.com/Mikubill/sd-webui-controlnet extensions/sd-webui-controlnet
RUN pip cache purge

CMD ["python", "launch.py", "--listen", "--api", "--xformers", "--disable-safe-unpickle"]