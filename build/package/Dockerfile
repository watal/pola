# Stage 1: Build
FROM golang:1.24.2-bookworm AS builder

WORKDIR /pola

# Copy source code and build Pola binary
COPY . .
RUN go install ./cmd/...

# Stage 2: Runtime
FROM debian:bookworm-slim
LABEL maintainer="Motoki TAKENAKA <m.takenaka@ntt.com>, Wataru Mishima <w.mishima@ntt.com>"

# Install packages and clean up
RUN apt-get update && apt-get install -y --no-install-recommends \
    bash-completion \
    iproute2 \
    vim \
    iputils-ping \
    && rm -rf /var/lib/apt/lists/*

# Copy the built binaries from the builder stage
COPY --from=builder /go/bin/pola /usr/local/bin/pola
COPY --from=builder /go/bin/polad /usr/local/bin/polad

# Enable bash completion in bashrc
RUN echo 'if [ -f /etc/bash_completion ] && ! shopt -oq posix; then' >> /root/.bashrc && \
    echo '  . /etc/bash_completion' >> /root/.bashrc && \
    echo 'fi' >> /root/.bashrc

# Install bash completion for Pola
RUN pola completion bash > /usr/share/bash-completion/completions/pola
