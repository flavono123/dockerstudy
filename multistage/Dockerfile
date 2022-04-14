FROM golang:1.18.1
WORKDIR /go
COPY app.go .
RUN go build app.go

FROM alpine:3.15.4
COPY --from=0 /go/app .
ENTRYPOINT ./app
