ARG BUILD_FROM
FROM $BUILD_FROM

ARG \
    PYTHON_VERSION \
    PIP_VERSION \
    CERT_IDENTITY \
    CERT_OIDC_ISSUER \
    QEMU_CPU

# ensure local python is preferred over distribution python
ENV PATH=/usr/local/bin:$PATH

# Set shell
SHELL ["/bin/ash", "-o", "pipefail", "-c"]

COPY *.patch /usr/src/
RUN set -ex \
    && export PYTHON_VERSION=${PYTHON_VERSION} \
    && apk add --no-cache --virtual .fetch-deps \
        openssl \
        tar \
        xz \
    && echo "${BUILD_FROM}" \
    && apk add --no-cache --virtual .cosign cosign \
        --repository="https://dl-cdn.alpinelinux.org/alpine/v3.21/community" \
    \
    && curl -L -o python.tar.xz "https://www.python.org/ftp/python/${PYTHON_VERSION%%[a-z]*}/Python-$PYTHON_VERSION.tar.xz" \
    && curl -L -o python.tar.xz.sigstore "https://www.python.org/ftp/python/${PYTHON_VERSION%%[a-z]*}/Python-$PYTHON_VERSION.tar.xz.sigstore" \
    && cosign verify-blob \
        --new-bundle-format \
        --certificate-identity "${CERT_IDENTITY}" \
        --certificate-oidc-issuer "${CERT_OIDC_ISSUER}" \
        --bundle python.tar.xz.sigstore \
        python.tar.xz
