# syntax=docker/dockerfile:1
FROM alpine:3.15.4
RUN export ADMIN_USER="mark" \
    && echo $ADMIN_USER > ./mark \
    && unset ADMIN_USER
