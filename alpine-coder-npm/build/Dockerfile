ARG NODE_VERSION=18                     #example 18.16.0 #node 18 highest supported version of code-server

# Stage 1: Build stage
FROM node:${NODE_VERSION}-alpine AS build-stage

# Install Python3 and build-essentials
RUN apk --no-cache add python3 krb5-dev build-base && \
    npm add -g code-server --unsafe-perm --legacy-peer-deps --omit=dev && \
    apk del python3 krb5-dev build-base

# Stage 2: Runtime stage
FROM node:${NODE_VERSION}-alpine

# Copy code-server
COPY --from=build-stage /usr/local/lib/node_modules/code-server /usr/local/lib/node_modules/code-server

# Install necessary packages
RUN apk --no-cache add doas bash gcompat libstdc++ curl grep openssh git && \
    corepack enable && corepack prepare pnpm@latest --activate && \
    ln -s /usr/local/lib/node_modules/code-server/out/node/entry.js /usr/local/bin/code-server

# Create a non-root user
ARG USER=coder
RUN printf "\n\n" | adduser -g ${USER} ${USER} -H -s /bin/bash && addgroup ${USER} wheel && \
    echo "permit setenv { PS1=$DOAS_PS1 SSH_AUTH_SOCK } :wheel" > /etc/doas.d/${USER}.conf

USER ${USER}
WORKDIR /home/${USER}
