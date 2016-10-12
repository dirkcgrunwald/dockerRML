
# RadioML Docker Image

A docker image provided by https://radioml.com/ which provides many of the primitives needed for radio machine learning experimentation.

## Docker Image Contents

 - **Base:**   Ubuntu 16.04 Xenial Xerus
 - **Remote:** ssh-server, x2go server + xfce4, ipython notebook
 - **Misc:**   screen, tmux, vim, emacs, git, meld
 - **DL:**     Theano, TensorFlow, Keras, OpenAI Gym, KeRLym
 - **ML:**     Scikit-learn, OpenCV, PyOpenPNL
 - **SDR:**    GNU Radio + several useful out-of-tree gr-modules

## Building the Container

Please note: your docker image max size must be >10GB for this build, please see Notes section.

```
git clone https://github.com/radioML/dockerRML.git rml
cd rml && sudo docker build -t radioml/radioml . 
```

This will take a while to build, so find something to do for an hour

## Running the Container

The container uses supervisord to start and ssh and jupyter 
notebook process to which you can connect. There are two
users, root/radioml and x2go/xgo. The x2go user has 'sudo'.

The standard way to use the system would

```
docker run --rm -it -p 2222:22 -p 8888:8888 -v $(pwd)/my-mnt:/home/x2go/mnt --name test_rml radioml/radioml
```

This starts the container with the SSH port (22) forwarded to localhost 2222
and the container Jupyter port (8888) forwarded to local port 8888.

When the container is running, you connect with the CLI using
```
sudo docker exec -i -t test_rml /bin/bash
```
or
```
ssh root@`docker port test_rml 22`
# use password radioml
```
or
```
ssh x2go@`docker port test_rml 22`
# use password x2go
```
It's recommended you use user `x2go`.

The x2go application ( http://wiki.x2go.org/doku.php ) lets
you access an XFCE4 desktop in the container. This is needed
if you want to use `gnuradio-companion`.

To connect with x2go
```
docker port test_rml 22
x2goclient
# set ssh ip and port from docker, login with x2go/x2go, use xfce as window manager
```
If you try to login with the root user, you'll get an error -- use `x2go` and then `sudo` instead.


Connect with iPython Notebook (good way to run python experiments),
```
docker port test_rml 88888
```
to determine the host and port for Jupyter
and then open http://docker_ip:8888 in the host browser.

## Using the Image

Launching GNU Radio Companion

```
gnuradio-companion
```

Running Keras Examples
```
cd /root/src/keras/examples
python mnist_mlp.py 
```

Running KeRLym Examples
```
cd /root/src/kerlym/examples
KERAS_BACKEND='tensorflow' ./run_breakout.sh
```

Running PyOpenPNL Examples
```
cd /root/src/PyOpenPNL/examples
./simple_bnet.py
```

## Notes

 - **GPU Support:** To build with GPU support for use with nvidia-docker, use dockerRML/full-GPU/Dockerfile 
 - **Image Size:** Current sizes are Full: 10.3GB, Full-GPU: 10.5GB, MinimalML: 4.0GB, MinimalSDR: 8.3GB
 - **Build Time:** Building Full on an 8 core i7-5930K within an RHEL 7.2 KVM instance on a non-SSD raid takes just over 2 hours, YMMV
 - **Docker BaseSize:** default docker basesize is 10GB, you must increase this to 20GB or 50GB by adding ' --storage-opt dm.basesize=50G ' to DOCKER_OPTS in /etc/default/docker or /etc/sysconfig/docker and restarting the docker daeming (**This must be done before starting the build**)



