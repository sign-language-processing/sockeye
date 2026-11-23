# Copyright 2018 Amazon.com, Inc. or its affiliates. All Rights Reserved.
#
# Licensed under the Apache License, Version 2.0 (the "License"). You may not
# use this file except in compliance with the License. A copy of the License
# is located at
#
#     http://aws.amazon.com/apache2.0/
#
# or in the "license" file accompanying this file. This file is distributed on
# an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either
# express or implied. See the License for the specific language governing
# permissions and limitations under the License.

FROM python:3.15.2-slim-trixie AS builder
RUN apt-get update && apt-get install -y --no-install-recommends cmake git g++ make \
    && rm -rf /var/lib/apt/lists/*
ARG FAST_ALIGN_COMMIT=7c2bbca3d5d61ba4b0f634f098c4fcf63c1373e1
RUN git clone https://github.com/clab/fast_align.git /opt/fast_align \
    && cd /opt/fast_align && git checkout "$FAST_ALIGN_COMMIT" \
    && cmake -S . -B build && cmake --build build --parallel 2 \
    && sed -i 's/stderr=subprocess.PIPE)/stderr=subprocess.PIPE, text=True, bufsize=1)/' build/force_align.py

FROM python:3.15.2-slim-trixie AS runtime
RUN apt-get update && apt-get upgrade -y \
    && apt-get install -y --no-install-recommends libstdc++6 libgomp1 \
    && rm -rf /var/lib/apt/lists/* /usr/local/lib/python3.15/ensurepip /usr/local/lib/python3.15/site-packages /usr/local/bin/pip*
COPY --from=builder /opt/fast_align/build/fast_align /opt/fast_align/build/atools /opt/fast_align/build/force_align.py /opt/fast_align/build/
COPY --from=builder /opt/fast_align/LICENSE.txt /usr/share/doc/fast_align/LICENSE.txt
ENV PATH="/opt/fast_align/build:$PATH"
CMD ["fast_align", "--help"]
