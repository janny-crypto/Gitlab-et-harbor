FROM golang:1.22-alpine AS build

WORKDIR /app

COPY go.mod go.sum ./
RUN go mod download

COPY src .

RUN go build -o server src/main.go

FROM alpine

WORKDIR /app

COPY --from=build /app/server .

EXPOSE 8080

CMD ["./server"]
