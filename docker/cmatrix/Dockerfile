# Build Container Image
FROM alpine as cmatrixbuilder

#MAINTAINER Shedrack Akintayo <akintayoshedrack@gmail.com>

LABEL org.opencontainers.image.authors="Shedrack Akintayo" \
  org.opencontainers.image.description="Container image for https://github.com/abishekvashok/cmatrix"

WORKDIR /cmatrix

RUN apk update --no-cache && \
  apk add git autoconf automake alpine-sdk ncurses-dev ncurses-static && \
  git clone https://github.com/spurin/cmatrix.git . && \
  autoreconf -i && \
  mkdir -p /usr/lib/kbd/consolefonts /usr/share/consolefonts && \
  ./configure LDFLAGS="-static" && \
  make

# cmatrix Container Image
FROM alpine

LABEL org.opencontainers.image.authors="Shedrack Akintayo" \
  org.opencontainers.image.description="Container image for https://github.com/abishekvashok/cmatrix"

RUN apk update --no-cache && \
  apk add ncurses-terminfo-base && \
  adduser -g "Shedrack Akintayo" -s /usr/sbin/nologin -D -H Shedrack

COPY --from=cmatrixbuilder /cmatrix/cmatrix /cmatrix

USER Shedrack

ENTRYPOINT ["./cmatrix"]
CMD ["-k", "-b", "-r"]
