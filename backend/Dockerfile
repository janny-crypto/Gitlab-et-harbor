FROM golang:1.22-alpine

WORKDIR /app
COPY go.mod ./src
RUN go mod download

COPY . .
RUN go build -o main .

CMD ["./main"]
