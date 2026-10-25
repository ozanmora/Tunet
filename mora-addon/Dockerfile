# Local Home Assistant add-on: builds Tunet from the ozanmora/Tunet "mora" branch
# (upstream main plus pending pull requests). Copy this folder to /addons/tunet_mora.
ARG TUNET_REPO=ozanmora/Tunet
ARG TUNET_REF=mora

# ── Stage 1: Build from source ────────────────────────────────────────
FROM node:26-alpine AS builder
ARG TUNET_REPO
ARG TUNET_REF
RUN apk add --no-cache curl tar python3 make g++
# Changes whenever the branch moves, so a rebuild never reuses a stale download.
ADD https://api.github.com/repos/${TUNET_REPO}/git/refs/heads/${TUNET_REF} /tmp/ref.json
RUN set -eux \
 && curl -fsSL "https://codeload.github.com/${TUNET_REPO}/tar.gz/refs/heads/${TUNET_REF}" -o /tmp/tunet.tar.gz \
 && mkdir -p /src \
 && tar -xzf /tmp/tunet.tar.gz --strip-components=1 -C /src \
 && cd /src \
 && npm ci \
 && npm run build

# ── Stage 2: Production image ────────────────────────────────────────
FROM ghcr.io/hassio-addons/base:21.0.0
ARG BUILD_VERSION
ARG BUILD_ARCH
LABEL \
	io.hass.version="${BUILD_VERSION}" \
	io.hass.type="app" \
	io.hass.arch="${BUILD_ARCH}"

RUN apk add --no-cache --upgrade musl nodejs npm python3 make g++

WORKDIR /app

COPY --from=builder /src/dist ./dist
COPY --from=builder /src/server ./server
COPY --from=builder /src/package.json .
COPY --from=builder /src/package-lock.json .

RUN npm ci --omit=dev \
 && apk del python3 make g++

COPY run.sh /run.sh
RUN chmod a+x /run.sh

CMD [ "/run.sh" ]
