# mcp/whatsapp-mcp-server/Dockerfile
FROM node:18-slim

WORKDIR /app

# Install Supergateway globally
RUN npm install -g supergateway

# Install Python, pip, FFmpeg, curl, and git
RUN apt-get update && \
    apt-get install -y --no-install-recommends python3 python3-pip ffmpeg curl git && \
    rm -rf /var/lib/apt/lists/*

# Install UV (Python package manager)
ARG UV_VERSION="0.1.36" # Check latest stable release
RUN curl -fSL "https://github.com/astral-sh/uv/releases/download/${UV_VERSION}/uv-x86_64-unknown-linux-musl.tar.gz" -o uv.tar.gz && \
    tar -xzf uv.tar.gz && \
    mv uv-x86_64-unknown-linux-musl/uv /usr/local/bin/uv && \
    rm -rf uv.tar.gz uv-x86_64-unknown-linux-musl
RUN /usr/local/bin/uv --version

# Copy the entire whatsapp-mcp cloned repository
COPY ./third_party/whatsapp-mcp/ ./third_party/whatsapp-mcp/

# --- Start of venv creation and dependency installation ---
ARG PROJECT_DIR=/app/third_party/whatsapp-mcp/whatsapp-mcp-server

RUN /usr/local/bin/uv venv --python /usr/bin/python3 ${PROJECT_DIR}/.venv

# Install all known required dependencies in one go
# This includes those from whatsapp-mcp-server's pyproject.toml AND mcp
RUN /usr/local/bin/uv pip install \
    --python ${PROJECT_DIR}/.venv/bin/python \
    "fastapi>=0.110.0" \
    "uvicorn>=0.29.0" \
    "pydantic>=2.6.4" \
    "aiofiles>=23.2.1" \
    "watchfiles>=0.21.0" \
    "python-dotenv>=1.0.1" \
    "sqlalchemy>=2.0.29" \
    "pillow>=10.3.0" \
    "python-magic>=0.4.27" \
    "mcp[cli]>=1.6.0" \
    "requests>=2.32.3" \
    "httpx>=0.28.1" \
    "urllib3" \
    --no-cache
# --- End of venv creation and dependency installation ---

EXPOSE 8004

CMD ["npx", "-y", "supergateway", "--stdio", "sh -c 'cd /app/third_party/whatsapp-mcp/whatsapp-mcp-server && .venv/bin/python main.py'", "--port", "8004"]