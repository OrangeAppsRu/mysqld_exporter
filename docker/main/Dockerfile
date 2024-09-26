FROM docker.io/golang:1.22 AS build

WORKDIR /app

COPY go.mod go.sum ./
RUN go mod download

COPY ./*.go ./
COPY ./collector ./collector
COPY ./percona ./percona
COPY ./tools ./tools

RUN CGO_ENABLED=0 GOOS=linux go build -ldflags="-s -w" -o ./mysqld_exporter .

FROM docker.io/alpine:3.20.3 AS certificates
RUN apk add --no-cache ca-certificates

FROM scratch
COPY --from=certificates /etc/ssl /etc/ssl
COPY --from=build /app/mysqld_exporter /mysqld_exporter
ENTRYPOINT ["/mysqld_exporter"]