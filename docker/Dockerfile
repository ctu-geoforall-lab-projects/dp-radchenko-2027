FROM ubuntu:26.10

RUN apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
        grass-core python3-venv locales tini ca-certificates curl unzip \
    && sed -i 's/^# *\(en_US.UTF-8 UTF-8\)/\1/' /etc/locale.gen \
    && locale-gen \
    && rm -rf /var/lib/apt/lists/*

# North Carolina dataset
ARG NC_DATASET_URL=https://grass.osgeo.org/sampledata/north_carolina/nc_basic_spm_grass7.zip
ENV GRASSDATA=/grassdata
RUN curl -fsSL -o /tmp/nc.zip "$NC_DATASET_URL" \
    && unzip -q /tmp/nc.zip -d "$GRASSDATA" \
    && rm /tmp/nc.zip \
    && chmod 1777 "$GRASSDATA"/* \
    && chmod a-w "$GRASSDATA"/*/PERMANENT

ENV PATH=/opt/venv/bin:$PATH
COPY grass-agent/requirements.txt /tmp/
RUN python3 -m venv /opt/venv \
    && pip install --no-cache-dir -r /tmp/requirements.txt

ENV HOME=/tmp USER=grass
USER ubuntu
WORKDIR /grass-agent
COPY --chown=ubuntu:ubuntu grass-agent/ .

ENTRYPOINT ["/usr/bin/tini", "--"]
CMD ["python3", "-m", "pytest", "tests/"]
