FROM --platform=linux/amd64 tensorflow/tensorflow:1.15.5-py3
 
ENV DEBIAN_FRONTEND=noninteractive
 
RUN apt-get update && apt-get install -y \
    git \
    cmake \
    vim \
    build-essential \
    automake \
    libtool \
    openjdk-8-jdk \
    locales \
    librsvg2-bin \
    && rm -rf /var/lib/apt/lists/*
 
RUN locale-gen en_US.UTF-8
ENV LANG=en_US.UTF-8
ENV LC_ALL=en_US.UTF-8
 
# Ensure `python` resolves to python3 (sequence_run.py uses `python`)
RUN ln -sf /usr/bin/python3 /usr/bin/python
 
RUN pip install --upgrade pip setuptools wheel
RUN pip install numpy==1.19.5 cython
 
WORKDIR /workspace
COPY requirements.txt .
RUN pip install --no-build-isolation -r requirements.txt
 
# Download NLTK + Benepar models at build time
RUN python - <<EOF
import nltk
import benepar
 
nltk.download('punkt', download_dir='/usr/local/nltk_data')
benepar.download('benepar_en2', download_dir='/usr/local/nltk_data')
EOF
 
ENV NLTK_DATA=/usr/local/nltk_data
 
# Copy the entire project so app.py and data/config are present
COPY . /workspace
 
WORKDIR /workspace/web
ENV FLASK_APP=app.py
EXPOSE 5000
CMD ["flask", "run", "--host=0.0.0.0", "--port=5000"]