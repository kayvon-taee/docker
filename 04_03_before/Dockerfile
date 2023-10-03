# HINT: Remember that you can find `ubuntu` images in the Docker Hub!
FROM ubuntu:xenial-20210804

COPY /app.sh /
RUN chmod +x /app.sh

ENTRYPOINT [ "/app.sh" ]
