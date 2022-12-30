FROM postgres:14.6

RUN set -eux; \
	apt-get update; \
	apt-get install -y --no-install-recommends ca-certificates wget; \
	rm -rf /var/lib/apt/lists/*;

RUN wget -O/usr/local/bin/wal-g-pg https://github.com/wal-g/wal-g/releases/download/v2.0.1/wal-g-pg-ubuntu-20.04-$(uname -m); chmod +x /usr/local/bin/wal-g-pg
