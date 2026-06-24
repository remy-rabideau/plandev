FROM alpine:3.15 AS extractor

COPY merlin-worker/build/distributions/*.tar /usr/src/app/server.tar
RUN mkdir /usr/src/app/extracted
RUN cd /usr/src/app && tar --strip-components 1 -xf server.tar -C extracted

FROM eclipse-temurin:21-jre-jammy

# Install Python 3.11 and pip
RUN apt-get update && apt-get install -y software-properties-common && \
    add-apt-repository ppa:deadsnakes/ppa && \
    apt-get update && \
    apt-get install -y python3.11 python3.11-venv python3-pip && \
    update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.11 1 && \
    update-alternatives --install /usr/bin/python python /usr/bin/python3.11 1 && \
    python3.11 -m ensurepip && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# Install py4j
RUN python3.11 -m pip install --no-cache-dir py4j

# Copy and install PyMerlin from local wheel
COPY pymerlin/*.whl /tmp/
RUN python3.11 -m pip install --no-cache-dir /tmp/*.whl && \
    rm -rf /tmp/*.whl

COPY --from=extractor /usr/src/app/extracted /usr/src/app

WORKDIR /usr/src/app
ENTRYPOINT ["/usr/src/app/bin/merlin-worker"]
