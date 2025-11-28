FROM golang:1.22-alpine
WORKDIR /app
COPY src .
RUN go build -o server src/main.go
CMD ["./main"]
