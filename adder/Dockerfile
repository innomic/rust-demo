FROM docker.io/library/rust:1.67-alpine as builder
RUN apk add --no-cache musl-dev sqlite-static openssl-dev openssl-libs-static pkgconf git libpq-dev

# Set `SYSROOT` to a dummy path (default is /usr) because pkg-config-rs *always*
# links those located in that path dynamically but we want static linking, c.f.
# https://github.com/rust-lang/pkg-config-rs/blob/54325785816695df031cef3b26b6a9a203bbc01b/src/lib.rs#L613
ENV SYSROOT=/dummy

# The env vars tell libsqlite3-sys to statically link libsqlite3.
ENV SQLITE3_STATIC=1 SQLITE3_LIB_DIR=/usr/lib/

# The env var tells pkg-config-rs to statically link libpq.
ENV LIBPQ_STATIC=1

WORKDIR /build
COPY . /build
RUN cargo build --bins --release --package adder

FROM scratch
COPY --from=builder /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/ca-certificates.crt
COPY --from=builder /build/target/release/adder /
CMD ["./adder"]
