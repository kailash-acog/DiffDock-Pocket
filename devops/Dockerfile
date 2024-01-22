# Base Image
FROM nvidia/cuda:11.7.1-devel-ubuntu22.04

LABEL maintainer="Kailash Lohar <kailash@aganitha.ai>"

# Set Working Directory
WORKDIR /home

# Install Utilities and Micromamba, Set Environment Path, Create Environment and Download dependencies
RUN apt-get update && apt-get install -y curl git && \
    mkdir -p /micromamba && \
    curl -Ls https://micro.mamba.pm/api/micromamba/linux-64/latest | tar -xj -C /micromamba bin/micromamba && \
    rm -rf /var/lib/apt/lists/*
ENV PATH /micromamba/bin:$PATH
COPY environment.yaml .
RUN micromamba create -y -f environment.yaml && micromamba clean --all --yes

# Download ESM models
RUN mkdir -p ~/.cache/torch/hub/checkpoints
ENV ESM_MODEL="esm2_t33_650M_UR50D"
ENV CONTACT_MODEL="$ESM_MODEL-contact-regression"
RUN curl -o ~/.cache/torch/hub/checkpoints/$ESM_MODEL.pt https://dl.fbaipublicfiles.com/fair-esm/models/$ESM_MODEL.pt
RUN curl -o ~/.cache/torch/hub/checkpoints/$CONTACT_MODEL.pt https://dl.fbaipublicfiles.com/fair-esm/regression/$CONTACT_MODEL.pt

# Expose Port
EXPOSE 7777

# Set Default Command
CMD ["micromamba", "run", "-n", "diffdock-pocket", "jupyter", "lab", "--ip=0.0.0.0", "--port=7777", "--no-browser", "--allow-root", "--NotebookApp.token=''", "--NotebookApp.password=''"]
