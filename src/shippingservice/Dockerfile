FROM golang:1.12-alpine as builder
RUN apk add --no-cache ca-certificates gcc libc-dev git

ENV PROJECT github.com/GoogleCloudPlatform/microservices-demo/src/shippingservice
WORKDIR /go/src/$PROJECT

# restore dependencies
COPY Gopkg.* ./
#RUN dep ensure --vendor-only -v
COPY . .
RUN go get -d ./...
RUN go install -ldflags '-linkmode external' .

FROM alpine as release
RUN apk add --no-cache ca-certificates
RUN GRPC_HEALTH_PROBE_VERSION=v0.2.0 && \
    wget -qO/bin/grpc_health_probe https://github.com/grpc-ecosystem/grpc-health-probe/releases/download/${GRPC_HEALTH_PROBE_VERSION}/grpc_health_probe-linux-amd64 && \
    chmod +x /bin/grpc_health_probe
COPY --from=builder /go/bin/shippingservice /shippingservice
ENV APP_PORT=50051
EXPOSE 50051
ENTRYPOINT ["/shippingservice"]
