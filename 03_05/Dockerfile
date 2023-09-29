# Where to pull images from
FROM ubuntu
# Label to provide info like maintainer
LABEL maintainer="Carlos Nunez <dev@carlosnunez.me>"
# By default user is root, but for security, best to use user nobody
USER root
# Copies files from docker build command to container image
# Directory provided to docker build is called context
# Context is usually working directory
COPY ./entrypoint.bash /
# Customise image
# Configure files or install software
RUN apt -y update
RUN apt -y install curl bash
RUN chmod 755 /entrypoint.bash
# Change default user so when program is finished, prevents
# user from potentially changing important files to host
USER nobody
# Tells docker what command containers created from this image should run
# Can also use CMD but there are differences.
ENTRYPOINT [ "/entrypoint.bash" ]
