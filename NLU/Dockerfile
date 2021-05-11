FROM ubuntu:18.04
RUN apt-get update
RUN apt-get install -y wget && rm -rf /var/lib/apt/lists/*

RUN apt-get update && apt-get install -y \
    tar \
    wget \
    bash \
    rsync \
    gcc \
    libfreetype6-dev \
    libhdf5-serial-dev \
    libpng-dev \
    libzmq3-dev \
    python3 \
    python3-dev \
    python3-pip \
    unzip \
    pkg-config \
    software-properties-common \
    graphviz \
    openjdk-8-jdk \
    ant


# SSH server related changes
# TODO exposed ports, need to fix
EXPOSE 2222
EXPOSE 6000
EXPOSE 8088
RUN apt update && \
  apt install -y \
  ca-certificates supervisor openssh-server bash ssh \
  curl wget vim procps htop locales nano man && \
  sed -i "s/# en_US.UTF-8/en_US.UTF-8/" /etc/locale.gen && \
  locale-gen && \
  useradd -m -u 13011 -s /bin/bash toolkit && \
  passwd -d toolkit && \
  useradd -m -u 13011 -s /bin/bash --non-unique console && \
  passwd -d console && \
  useradd -m -u 13011 -s /bin/bash --non-unique _toolchain && \
  passwd -d _toolchain && \
  useradd -m -u 13011 -s /bin/bash --non-unique coder && \
  passwd -d coder && \
  chown -R toolkit:toolkit /run /etc/shadow && \
  apt autoremove --purge && apt-get clean && \
  rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
  echo ssh >> /etc/securetty && \
  rm -f /etc/legal /etc/motd


RUN mkdir -p /app && chown -R toolkit:toolkit /app
USER toolkit
WORKDIR /app
RUN cd /app && wget \
    https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh \
    && mkdir /app/.conda \
    && bash Miniconda3-latest-Linux-x86_64.sh -b \
    && rm -f Miniconda3-latest-Linux-x86_64.sh 

ENV PATH="/home/toolkit/miniconda3/bin:${PATH}"
ARG PATH="/home/toolkit/miniconda3/bin:${PATH}"
RUN conda install python=3.7
RUN conda config --set notify_outdated_conda false \
  && conda install poetry==1.1.2 -c conda-forge -y \
  && poetry config virtualenvs.create false

COPY pyproject.toml /app
COPY poetry.lock /app
COPY . /app
RUN poetry install


ENV LANG=en_US.UTF-8
ENV PYSPARK_PYTHON=python3
ENV PYSPARK_DRIVER_PYTHON=python3
ENV JUPYTER_RUNTIME_DIR /tmp
ENV HOME /home/toolkit
ENV JAVA_HOME /usr/lib/jvm/java-8-openjdk-amd64/
RUN export JAVA_HOME
RUN echo "export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64/" >> ~/.bashrc

# Specify the default command to run
CMD ["jupyter", "notebook", "--ip", "0.0.0.0"]