ARG cuda_version=9.0
ARG cudnn_version=7
FROM nvidia/cuda:${cuda_version}-cudnn${cudnn_version}-devel

# Install packages
RUN apt-get update && apt-get upgrade -y && apt-get dist-upgrade -y && apt-get update && apt-get install -y --no-install-recommends \
      bzip2 unzip inetutils-ping \
      g++ git pkg-config \
      libgl1-mesa-glx libhdf5-dev openmpi-bin \
      wget x11vnc fluxbox xvfb graphviz \
      libopencv-core-dev libopencv-flann-dev libopencv-imgproc-dev libopencv-photo-dev libopencv-video-dev libopencv-features2d-dev \
      libopencv-objdetect-dev libopencv-calib3d-dev libopencv-ml-dev libopencv-contrib-dev libopencv-dev

# Install conda
ENV CONDA_DIR /opt/conda
ENV PATH $CONDA_DIR/bin:$PATH

RUN wget --quiet --no-check-certificate https://repo.continuum.io/miniconda/Miniconda3-4.2.12-Linux-x86_64.sh && \
    echo "c59b3dd3cad550ac7596e0d599b91e75d88826db132e4146030ef471bb434e9a *Miniconda3-4.2.12-Linux-x86_64.sh" | sha256sum -c - && \
    /bin/bash /Miniconda3-4.2.12-Linux-x86_64.sh -f -b -p $CONDA_DIR && \
    rm Miniconda3-4.2.12-Linux-x86_64.sh && \
    echo export PATH=$CONDA_DIR/bin:'$PATH' > /etc/profile.d/conda.sh

# Install Python packages and keras
ENV NB_USER keras
ENV NB_UID 1000

RUN useradd -m -s /bin/bash -N -u $NB_UID $NB_USER && \
    chown $NB_USER $CONDA_DIR -R && \
    mkdir -p /src && \
    chown $NB_USER /src

USER $NB_USER

ARG python_version=3.6

RUN conda config --append channels conda-forge
RUN conda install -y python=${python_version} && \
    pip install --upgrade pip && \
    pip install \
      sklearn_pandas \
      tensorflow-gpu==1.12.0 \
      cntk-gpu \
      opencv-python \
      seaborn && \
    conda install \
      bcolz \
      h5py \
      matplotlib \
      mkl \
      nose \
      notebook \
      Pillow \
      pandas \
      pydot \
      pygpu \
      pyyaml \
      scikit-learn \
      six \
      theano \
      mkdocs \
      && \
    git clone https://github.com/keras-team/keras.git /src && pip install -e /src[tests] && \
    pip install git+https://github.com/keras-team/keras.git && \
    conda clean -yt

ADD theanorc /home/keras/.theanorc
ADD jupyter_notebook_config.py /home/keras/.jupyter/jupyter_notebook_config.py

ENV LC_ALL=C.UTF-8
ENV LANG=C.UTF-8

ENV PYTHONPATH='/src/:$PYTHONPATH'

WORKDIR /data

EXPOSE 8888

CMD jupyter notebook --port=8888 --ip=0.0.0.0
