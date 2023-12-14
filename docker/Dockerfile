# -- multistage docker build: stage #1: build stage
FROM golang:1.19-alpine AS build

RUN mkdir -p /go/src/github.com/Pyrinpyi/pyipad

WORKDIR /go/src/github.com/Pyrinpyi/pyipad

RUN apk add --no-cache curl git openssh binutils gcc musl-dev

COPY go.mod .
COPY go.sum .


# Cache pyipad dependencies
RUN go mod download

COPY . .

RUN go build $FLAGS -o pyipad .

# --- multistage docker build: stage #2: runtime image
FROM alpine

RUN addgroup -g 1000 pyrin && \
    adduser -u 1000 -G pyrin -D pyrin

WORKDIR /app

RUN apk add --no-cache ca-certificates

COPY --from=build /go/src/github.com/Pyrinpyi/pyipad/pyipad /app/
COPY --from=build /go/src/github.com/Pyrinpyi/pyipad/infrastructure/config/sample-pyipad.conf /app/

USER pyrin
ENTRYPOINT [ "/app/pyipad", "/utxoindex", "--appdir", "/data" ]
