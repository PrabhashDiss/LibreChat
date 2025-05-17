# mcp/whatsapp-go-bridge/Dockerfile
FROM golang:1.24.1-alpine AS builder

# Set the working directory for the bridge source code within the container's build stage
WORKDIR /app/build/whatsapp-bridge

# Install build tools
RUN apk add --no-cache gcc musl-dev sqlite-dev

# Copy go.mod and go.sum first to leverage Docker cache
# Source path is relative to the build context (project root)
# Destination path is relative to WORKDIR (/app/build/whatsapp-bridge)
COPY ./third_party/whatsapp-mcp/whatsapp-bridge/go.mod ./third_party/whatsapp-mcp/whatsapp-bridge/go.sum ./
RUN go mod download

# Copy the rest of the whatsapp-bridge application source code
# Source path is relative to the build context (project root)
# Destination path is relative to WORKDIR (/app/build/whatsapp-bridge)
COPY ./third_party/whatsapp-mcp/whatsapp-bridge/ ./

# Build the Go application
# CGO_ENABLED=1 is important for go-sqlite3
# The build command is run from WORKDIR (/app/build/whatsapp-bridge)
RUN CGO_ENABLED=1 GOOS=linux go build -o /whatsapp-bridge-server main.go

# --- Final image ---
FROM alpine:latest

RUN apk --no-cache add ca-certificates sqlite

# Copy the compiled binary from the builder stage
COPY --from=builder /whatsapp-bridge-server /whatsapp-bridge-server

# WORKDIR for the running application.
# The Go app, by default, creates its "store" subdir in the current working directory.
WORKDIR /app

# Ensure the /app/store directory exists for the volume mount.
# The Go application will write into /app/store/
RUN mkdir -p /app/store

# Command to run the application
# It will create its database in /app/store/
CMD ["/whatsapp-bridge-server"]