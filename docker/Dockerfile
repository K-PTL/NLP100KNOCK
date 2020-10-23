FROM py_base_on_ubu18:3.8.3

ENV PYTHON_VERSION 3.8.3
ENV DEBIAN_FRONTEND=noninteractive
ENV HOME /root
ENV PYTHON_ROOT $HOME/local/python-$PYTHON_VERSION
ENV PATH $PYTHON_ROOT/bin:$PATH
ENV PYENV_ROOT $HOME/.pyenv

# install packages in requirements.txt
ADD requirements.txt ./
RUN pip install -r requirements.txt    # requirements.txtからパッケージのインストール

# below codes are referenced; https://system.blog.uuum.jp/entry/2019/10/18/110000
# mecabとmecab-ipadic-NEologdの導入; see here -> https://github.com/neologd/mecab-ipadic-neologd/blob/master/README.ja.md
RUN apt-get update \
    && apt-get install -y mecab \
    && apt-get install -y libmecab-dev \
    && apt-get install -y mecab-ipadic-utf8 \
    && apt-get install -y xz-utils \
    && apt-get install -y file 

# see here -> https://stackoverflow.com/questions/30716937/dockerfile-build-possible-to-ignore-error
RUN git clone --depth 1 https://github.com/neologd/mecab-ipadic-neologd.git ; exit 0
RUN cd mecab-ipadic-neologd \
    && bin/install-mecab-ipadic-neologd -n -y

RUN pip install mecab-python3

# nodejsの導入
RUN curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash - \
    && sudo apt-get install -y nodejs

## JupyterLabの拡張機能
RUN jupyter labextension install @lckr/jupyterlab_variableinspector \
    && jupyter labextension install @jupyterlab/toc

# CRF++ https://github.com/ironball1113/tutorials/blob/master/docker/nlp-sandbox/Dockerfile
RUN wget -O /tmp/CRF++-0.58.tar.gz 'https://drive.google.com/uc?export=download&id=0B4y35FiV1wh7QVR6VXJ5dWExSTQ' \
    && cd /tmp/ \
    && tar zxf CRF++-0.58.tar.gz \
    && cd CRF++-0.58 \
    && ./configure \
    && make \
    && make install

RUN apt-get install -y \
    cron \
    vim \
    tree \
    language-pack-ja

# CaboCha
RUN cd /tmp \
  && curl -c cabocha-0.69.tar.bz2 -s -L "https://drive.google.com/uc?export=download&id=0B4y35FiV1wh7SDd1Q1dUQkZQaUU" \
    | grep confirm | sed -e "s/^.*confirm=\(.*\)&amp;id=.*$/\1/" \
    | xargs -I{} curl -b  cabocha-0.69.tar.bz2 -L -o cabocha-0.69.tar.bz2 \
      "https://drive.google.com/uc?confirm={}&export=download&id=0B4y35FiV1wh7SDd1Q1dUQkZQaUU" \
  && tar jxf cabocha-0.69.tar.bz2 \
  && cd cabocha-0.69 \
  && export CPPFLAGS=-I/usr/local/include \
  && ./configure --with-mecab-config=`which mecab-config` --with-charset=utf8 \
  && make \
  && make install \
  && cd python \
  && python3 setup.py build \
  && python3 setup.py install \
  && cd / \
  && rm /tmp/cabocha-0.69.tar.bz2 \
  && rm -rf /tmp/cabocha-0.69 \
  && ldconfig
