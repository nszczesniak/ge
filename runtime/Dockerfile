########## Pull base image.
FROM bilsalak/base:os_ubuntu_20.04

RUN \
export DEBIAN_FRONTEND=noninteractive && \
########## Update
  apt-get update && \
########## Setup dev user
  apt-get install sudo && \
  useradd -G sudo,staff -md /home/developer -s /bin/bash developer && \
  echo "developer:password" | chpasswd && \
########## Install great_expectations
  apt-get install python3 -y && \
  apt-get install python3-pip -y && \
  pip3 install great_expectations && \
########## Setup directory structure
  mkdir /home/project && \
  chown developer:developer /home/project && \
  mkdir /home/data && \
##########
########## Cleanup
##########
  rm -rf /tmp/* && \
  apt-get clean

##########
########## Helper commands
##########
COPY helper_commands/notebook /usr/bin/notebook

#Default to developer user
USER developer

EXPOSE 8888

CMD /opt/startup_manager/init.sh