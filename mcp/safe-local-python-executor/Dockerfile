FROM node:18-slim

# Avoid interactive prompts during package installs
ENV DEBIAN_FRONTEND=noninteractive

# Install OS packages: Python 3, venv support, curl, certificates
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        python3 \
        python3-venv \
        curl \
        ca-certificates \
        lsb-release \
    && rm -rf /var/lib/apt/lists/*

# Create a virtual environment for uv and install via pip
ENV VENV_DIR="/opt/uv"
RUN python3 -m venv "$VENV_DIR" \
    && "$VENV_DIR/bin/pip" install --no-cache-dir --upgrade pip \
    && "$VENV_DIR/bin/pip" install --no-cache-dir uv

# Add uv venv bin to PATH and verify installation
ENV PATH="${VENV_DIR}/bin:${PATH}"
RUN uv --version

# Install supergateway globally
RUN npm install -g supergateway

# Set working directory for your executor code
WORKDIR /app/executor_code

# Expose the port Supergateway will listen on
EXPOSE 8005

# Launch sequence:
# 1) Show shared library dependencies of uv
# 2) Start supergateway, invoking uv through stdio
CMD ["sh", "-c", "echo '--- Running ldd on $(which uv) ---'; \
    ldd $(which uv); \
    echo '--- Attempting to start Supergateway ---'; \
    npx -y supergateway --stdio \"uv run mcp_server.py\" --port 8005"]
