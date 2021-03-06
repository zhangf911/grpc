# Copyright 2015, Google Inc.
# All rights reserved.
#
# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions are
# met:
#
#     * Redistributions of source code must retain the above copyright
# notice, this list of conditions and the following disclaimer.
#     * Redistributions in binary form must reproduce the above
# copyright notice, this list of conditions and the following disclaimer
# in the documentation and/or other materials provided with the
# distribution.
#     * Neither the name of Google Inc. nor the names of its
# contributors may be used to endorse or promote products derived from
# this software without specific prior written permission.
#
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
# "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
# LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR
# A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT
# OWNER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,
# SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT
# LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE,
# DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY
# THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
# (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
# OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

# Dockerfile for gRPC Go
FROM golang:1.4

# Install SSH to that Go source can be pulled securely.
RUN apt-get update && apt-get install -y ssh

# Install a GitHub SSH service credential that gives access to the GitHub repo while it's private
#
# TODO: remove this once the repo is public
ADD .ssh .ssh
RUN chmod 600 /.ssh/github.rsa
RUN mkdir -p $HOME/.ssh && echo 'Host github.com' > $HOME/.ssh/config
RUN echo "    IdentityFile /.ssh/github.rsa" >> $HOME/.ssh/config
RUN echo 'StrictHostKeyChecking no' >> $HOME/.ssh/config

# Force go get to use the GitHub ssh url instead of https, and use the SSH creds
RUN git config --global url."git@github.com:".insteadOf "https://github.com/"

# Get the source from GitHub
RUN go get google.golang.org/grpc

# Add a service_account directory containing the auth creds file
ADD service_account service_account

# Build the interop client and server
RUN cd src/google.golang.org/grpc/interop/client && go install
RUN cd src/google.golang.org/grpc/interop/server && go install

# Specify the default command such that the interop server runs on its known testing port
CMD ["/bin/bash", "-c", "cd src/google.golang.org/grpc/interop/server && go run server.go --use_tls=true --port=8020"]
