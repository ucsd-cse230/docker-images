# Image based on
# https://github.com/PrairieLearn/PrairieLearn/blob/master/graders/python/Dockerfile
FROM amazonlinux:2023

# Install dependencies
RUN dnf -y update
RUN dnf install -y util-linux python3.12 tar gzip git

# Set default python
RUN alternatives --install /usr/bin/python3 python3 /usr/bin/python3.12 1

# create a non-root user and install Lean on it
RUN useradd -m grader
USER grader
WORKDIR /home/grader
RUN curl https://raw.githubusercontent.com/leanprover/elan/master/elan-init.sh -sSf | sh -s -- -y --default-toolchain leanprover/lean4:4.15.0

# The grader needs to run on `root` to be able to write `results.json` in `/grade/results`
USER root
