# GPDB 5 Demo

**January, 2019**


This was built on top of demo by Jarrod Vawdrey (jvawdrey@pivotal.io)

-rpb (rbennett@pivotal.io)

**January, 2018**

#### Initial items to cover

* Jupyter Notebook and connecting to GPDB
* Apache MADlib
* PL/Python
* GPDB-Spark Connector



### Option 1 Quick-Start: Use images from dockerhub
** Quick-Start and Build instructions assume that you have docker installed and running**

1. Start Docker-Machine
```bash
    eval "$(docker-machine env default)"
```
2. Pull Images
```bash
    docker pull rpbennett/gpdb
    docker pull rpbennett/jupyter
    docker pull rpbennett/juliaopt
```
3. Run Containers
   * Run each container in a separate terminal
   * The Jupyter container must be run from the docker-jupyter directory
```bash

   # create network for inter container communication
   docker network create -d bridge contbridge

   # run gpdb image terminal
   docker run -it --rm --network=contbridge -p 5432:5432 --name=gpdb rpbennett/gpdb

   # build spark container terminal
   docker run -it --rm --network=contbridge -p 4040:4040 -p 8080:8080 -p 8081:8081 -h spark --name=spark p7hb/docker-spark
   # from within container
   start-master.sh && start-slave.sh spark://spark:7077

   # run jupyter-notebook container ternminal (from docker-jupyter directory)
   cd docker-jupyter
   docker run -it --rm --network=contbridge -p 8888:8888 --name=jupyter --mount type=bind,source=$(pwd)/notebooks,destination=/jupyter/notebooks rpbennett/jupyter
```

4. Docker IP

```bash
# Grab IP of 'default' image
docker-machine ip default
# 192.168.99.100

```

* Jupyter Notebook: http://< IP ADDRESS >:8888/
* Spark Master WebUI console: http://< IP ADDRESS >:8080/
* Spark Worker WebUI console: http://< IP ADDRESS >:8081/
* Spark WebUI console: http://< IP ADDRESS >:4040/


### Option 2: Build Docker Images

**Instructions assume that you have docker installed and running**

1. Download the following files to "docker-gpdb/pivotal" directory from https://network.pivotal.io/products/pivotal-gpdb
  * Greenplum Database 5.3.0 Binary Installer for RHEL 6 (greenplum-db-5.3.0-rhel6-x86_64.zip)
  * MADlib 1.13 for RHEL 6 (madlib-1.13-gp5-rhel6-x86_64.tar.gz)
  * Python Data Science Package for RHEL 6 (DataSciencePython-1.1.1-gp5-rhel6-x86_64.gppkg)
  * PostGis PostGis extensions for GPDB (postgis-2.1.5-gp5-rhel6-x86_64.gppkg)

2. Download the following files to "docker-jupyter/pivotal" directory from https://network.pivotal.io/products/pivotal-gpdb
  * Greenplum-Spark Connector 1.1.0 (greenplum-spark_2.11-1.1.0.jar)

3. Build containers
```bash
# Grab IP address of docker container
# I am using docker / virtualbox setup where command is
eval "$(docker-machine env default)"

# build gpdb container
cd docker-gpdb
docker build -t rpbennett/gpdb .
cd ..

# build jupyter container
cd docker-jupyter
docker build -t rpbennett/jupyter .
cd ..

```

4. Run containers
```bash

# create network for inter container communication
docker network create -d bridge contbridge

# run gpdb image
docker run -it --rm --network=contbridge -p 5432:5432 --name=gpdb rpbennett/gpdb

# build spark container
docker run -it --rm --network=contbridge -p 4040:4040 -p 8080:8080 -p 8081:8081 -h spark --name=spark p7hb/docker-spark
# from within container
start-master.sh && start-slave.sh spark://spark:7077

# run jupyter-notebook container (from docker-jupyter directory)
cd docker-jupyter
docker run -it --rm --network=contbridge -p 8888:8888 --name=jupyter --mount type=bind,source=$(pwd)/notebooks,destination=/jupyter/notebooks rpbennett/jupyter

```

5. Docker IP
```bash
# Grab IP of 'default' image
docker-machine ip default
# 192.168.99.100

```

* Jupyter Notebook: http://< IP ADDRESS >:8888/
* Spark Master WebUI console: http://< IP ADDRESS >:8080/
* Spark Worker WebUI console: http://< IP ADDRESS >:8081/
* Spark WebUI console: http://< IP ADDRESS >:4040/


### Modeling Workflow Example

docker-jupyter/notebooks/Modeling Workflow Example - Greenplum Database.ipynb

1) Docker image: See instructions below
2) Greenplum cluster: To run notebook against Greenplum cluster
  * Greenplum Environment: GPDB 5.3, MADlib 1.13, Greenplum Python Data Science Package
  * Python packages required for Jupyter Notebook: psycopg2, pandas, seaborn, textwrap, ipywidgets, IPython




### Troubleshooting Known Issues

Configuring shell session
```bash

# check docker is available
docker --version
# Docker version 1.9.1, build a34a1d5
# Docker version 1.8.0, build 0d03096

docker run -it --rm -p 8888:8888 jupyter/pyspark-notebook
# Cannot connect to the Docker daemon. Is the docker daemon running on this host?

# Run this command to configure your shell:
eval "$(docker-machine env default)"

# Error checking TLS connection: default is not running. Please start it in order to use the connection settings
docker-machine rm default
docker-machine create default --driver virtualbox
# Running pre-create checks...
# Creating machine...
# (default) Creating VirtualBox VM...
# (default) Creating SSH key...
# Error attempting heartbeat call to plugin server: connection is shut down
# Error creating machine: Error in driver during machine creation: unexpected EOF

```

```bash

eval "$(docker-machine env default)"
# Error checking TLS connection: Error checking and/or regenerating the certs: There was an error validating certificates for
# host "192.168.99.100:2376": x509: certificate is valid for 192.168.99.101, not 192.168.99.100
# You can attempt to regenerate them using 'docker-machine regenerate-certs [name]'.
# Be advised that this will trigger a Docker daemon restart which might stop running containers.

docker-machine regenerate-certs default
# Regenerate TLS machine certs?  Warning: this is irreversible. (y/n): y
# Regenerating TLS certificates
# Waiting for SSH to be available...
# Detecting the provisioner...
# Copying certs to the local machine directory...
# Copying certs to the remote machine...
# Setting Docker configuration on the remote daemon...

eval "$(docker-machine env default)"
```

#### Contact

* Robert Bennett(rbennett@pivotal.io) 
