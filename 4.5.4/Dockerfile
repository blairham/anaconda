FROM alpine as miniconda
MAINTAINER Blair Hamilton <blairham@wickedcode.io>

# install miniconda
RUN set -eux \
  ; apk --no-cache add ca-certificates wget \
  ; apk add --no-cache bash

# install glibc
RUN set -eux \
  ; apk --no-cache add ca-certificates wget \
  ; wget -q -O /etc/apk/keys/sgerrand.rsa.pub https://raw.githubusercontent.com/sgerrand/alpine-pkg-glibc/master/sgerrand.rsa.pub \
  ; wget https://github.com/sgerrand/alpine-pkg-glibc/releases/download/2.27-r0/glibc-2.27-r0.apk \
  ; wget https://github.com/sgerrand/alpine-pkg-glibc/releases/download/2.27-r0/glibc-bin-2.27-r0.apk \
  ; wget https://github.com/sgerrand/alpine-pkg-glibc/releases/download/2.27-r0/glibc-i18n-2.27-r0.apk \
  ; apk add --no-cache glibc-2.27-r0.apk \
  ; apk add --no-cache glibc-bin-2.27-r0.apk \
  ; apk add --no-cache glibc-i18n-2.27-r0.apk \
  ; /usr/glibc-compat/bin/localedef --force --inputfile POSIX --charmap UTF-8 C.UTF-8 || true \
  ; echo "export LANG=C.UTF-8" > /etc/profile.d/locale.sh

RUN set -eux \
  ; wget --quiet https://repo.anaconda.com/miniconda/Miniconda2-4.5.4-Linux-x86_64.sh -O ~/miniconda.sh && \
  /bin/bash ~/miniconda.sh -b -p /opt/conda

#########################################################################
# from miniconda
FROM alpine
MAINTAINER Blair Hamilton <blairham@wickedcode.io>

# bring over needed installs from miniconda stage
COPY --from=miniconda /opt/conda /opt/conda
COPY --from=miniconda /glibc-2.27-r0.apk /glibc-2.27-r0.apk
COPY --from=miniconda /glibc-bin-2.27-r0.apk /glibc-bin-2.27-r0.apk
COPY --from=miniconda /glibc-i18n-2.27-r0.apk /glibc-i18n-2.27-r0.apk
COPY --from=miniconda /etc/apk/keys/sgerrand.rsa.pub /etc/apk/keys/sgerrand.rsa.pub

# add conda to path
ENV PATH=$PATH:/opt/conda:/opt/conda/bin \
    LANG=C.UTF-8

RUN set -eux \
  ; apk update \
  ; apk upgrade \
  ; apk add --no-cache tini \
  ; apk add --no-cache bash \
  ; apk add --no-cache bash-completion \
#
  ; apk add glibc-2.27-r0.apk \ 
  ; apk add --no-cache glibc-2.27-r0.apk \
  ; apk add --no-cache glibc-bin-2.27-r0.apk \
  ; apk add --no-cache glibc-i18n-2.27-r0.apk \
  ; /usr/glibc-compat/bin/localedef --force --inputfile POSIX --charmap UTF-8 C.UTF-8 || true \
  ; rm glibc*-2.27-r0.apk \
  ; echo "export LANG=C.UTF-8" > /etc/profile.d/locale.sh
  
# configure anaconda
RUN set -eux \
# setup conda
  ; ln -s /opt/conda/etc/profile.d/conda.sh /etc/profile.d/conda.sh \
  ; echo ". /opt/conda/etc/profile.d/conda.sh" >> ~/.bashrc \
  ; echo "conda activate base" >> ~/.bashrc

ENTRYPOINT [ "tini", "--" ]
CMD ["/bin/bash"]
