FROM golang:1.24-alpine AS gobuild

WORKDIR /build
ADD . /build

RUN go get -d -v ./...
RUN CGO_ENABLED=0 GOOS=linux go build -a -ldflags '-extldflags "-static"' -o ./s3driver ./cmd/s3driver

FROM debian:bookworm-slim
LABEL maintainers="Cyrill Troxler <cyrilltroxler@gmail.com>"
LABEL description="csi-s3 slim image"

# s3fs and some other dependencies
RUN apt-get update && \
  apt-get install -y \
  s3fs curl unzip && \
  rm -rf /var/lib/apt/lists/*

# install rclone
RUN ARCH=$(dpkg --print-architecture) && \
    curl -o /tmp/rclone.deb -s https://downloads.rclone.org/rclone-current-linux-$ARCH.deb && \
    dpkg -i /tmp/rclone.deb && \
    rm /tmp/rclone.deb

COPY --from=gobuild /build/s3driver /s3driver
ENTRYPOINT ["/s3driver"]
