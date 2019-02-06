FROM ubuntu:14.04

COPY /pivotal/* /spark-jars/
COPY /sql/* jupyter/sql/

ENV ANACONDA_VER Anaconda2-5.0.1-Linux-x86_64

RUN apt-get update && apt-get install -yq --no-install-recommends \
    build-essential \
    emacs \
    git \
    inkscape \
    jed \
    libsm6 \
    libxext-dev \
    libxrender1 \
    lmodern \
    python-dev \
    vim \
    unzip \
    wget \
    default-jdk \
    scala \
  && wget --quiet --no-check-certificate https://repo.continuum.io/archive/${ANACONDA_VER}.sh \
  && bash ${ANACONDA_VER}.sh -b -p ~/anaconda \
  && rm ${ANACONDA_VER}.sh \
  && echo 'export PATH="~/anaconda/bin:$PATH"' >> ~/.bash_profile \
  && /bin/bash -c "source ~/.bash_profile" \
  && /bin/bash -c "~/anaconda/bin/conda update -y conda" \
  && /bin/bash -c "~/anaconda/bin/conda install -y pyspark psycopg2" \
  && apt-get install -y software-properties-common python-software-properties debconf-utils \
  && echo -ne '\n' | add-apt-repository ppa:webupd8team/java \
  && apt-get update \
  && echo "oracle-java8-installer shared/accepted-oracle-license-v1-1 select true" | debconf-set-selections \
  && apt-get install -y oracle-java8-installer \
  && wget --quiet --no-check-certificate http://apache.cs.utah.edu/spark/spark-2.2.0/spark-2.2.0-bin-hadoop2.7.tgz \
  && tar xvf spark-2.2.0-bin-hadoop2.7.tgz \
  && rm spark-2.2.0-bin-hadoop2.7.tgz \
  && mv spark-2.2.0-bin-hadoop2.7/ /root/spark

ENV TINI_VERSION v0.6.0
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /usr/bin/tini
RUN chmod +x /usr/bin/tini

ENV PATH "/root/anaconda/bin:${PATH}"
ENV PYSPARK_DRIVER_PYTHON ipython
ENV PYSPARK_DRIVER_PYTHON_OPTS 'notebook --port=8888 --no-browser --allow-root --ip=0.0.0.0 --notebook_dir=/notebooks --NotebookApp.iopub_data_rate_limit=100000000'
ENV SPARK_HOME /root/spark

VOLUME jupyter/notebooks
WORKDIR /jupyter

EXPOSE 8888

ENTRYPOINT ["tini", "--"]
CMD ["pyspark", "--master", "spark://spark:7077","--jars=../../spark-jars/greenplum-spark_2.11-1.1.0.jar","--driver-java-options","-Dderby.system.home=/"]
