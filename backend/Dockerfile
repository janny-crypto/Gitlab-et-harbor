FROM golang:1.22-alpine

WORKDIR /app

# Copier UNIQUEMENT go.mod et go.sum d'abord
COPY go.mod ./

# Télécharger les dépendances
RUN go mod download

# Ensuite copier le reste du code
COPY . .

# Compiler
RUN go build -o main .

CMD ["./main"]
