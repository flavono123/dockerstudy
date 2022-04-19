# syntax=docker/dockerfile:1
FROM alpine:3.15.4
LABEL name=layer
EXPOSE 3456
ENV APP=layer
ADD add.tar.gz /
COPY copy /copy
ENTRYPOINT ["date"]
RUN rm /bin/arch
CMD ["--help"]
VOLUME /log
USER root
ARG workdir
WORKDIR $workdir
ONBUILD RUN echo 'b'
STOPSIGNAL SIGTERM
HEALTHCHECK CMD which date
SHELL ["/bin/sh", "-c"]
