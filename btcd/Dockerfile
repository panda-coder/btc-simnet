FROM golang:1.22.2-alpine as build

LABEL maintainer="Ercy Moreira Neto <ercym.neto@gmail.com>"

RUN apk add --no-cache git gcc musl-dev

WORKDIR $GOPATH/src/github.com/btcsuite/btcd

ARG BTCD_VERSION=v0.24.0

RUN git clone https://github.com/btcsuite/btcd.git . \    
    && go install -v . ./cmd/...




# Start a new image
FROM alpine

EXPOSE 18555 18556

EXPOSE 28901 28902

# Copy the compiled binaries from the builder image.
COPY --from=build /go/bin/addblock /bin/
COPY --from=build /go/bin/btcctl /bin/
COPY --from=build /go/bin/btcd /bin/
COPY --from=build /go/bin/findcheckpoint /bin/
COPY --from=build /go/bin/gencerts /bin/

COPY ./start-btcctl.sh .
COPY ./start-btcd.sh .

RUN apk add --no-cache \
    bash \
    ca-certificates \
&&  mkdir "/rpc" "/root/.btcd" "/root/.btcctl" \
&&  touch "/root/.btcd/btcd.conf" \
&&  chmod +x start-btcctl.sh \
&&  chmod +x start-btcd.sh \
&& "/bin/gencerts" --host="*" --directory="/rpc" --force

VOLUME ["/rpc", "/data"]