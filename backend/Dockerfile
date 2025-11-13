# Étape de build
FROM golang:1.20-alpine AS builder
WORKDIR /app
COPY src/ .
RUN go build -o backend .

# Étape finale
FROM harbor.host:8086/library/alpine:latest
WORKDIR /root/
COPY --from=builder /app/backend .
EXPOSE 8080
CMD ["./backend"]
