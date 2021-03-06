FROM debian:stretch
MAINTAINER DocMarty84 <nicolas.martinelli@gmail.com>

# Set up user and mount point
RUN set -x; \
        useradd -m koozic && \
        mkdir -p /mnt/host && \
        chown -R koozic /mnt/host

# Reconfigure locales so PostgreSQL uses UTF-8 encoding
RUN set -x; \
        apt-get update -qq && \
        apt-get install -y --no-install-recommends -qq locales && \
        apt-get clean -qq
RUN set -x; \
        dpkg-reconfigure -fnoninteractive locales && \
        sed -i 's/# en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/g' /etc/locale.gen && \
        locale-gen en_US.UTF-8 && \
        /usr/sbin/update-locale LANG=en_US.UTF-8
ENV LC_ALL en_US.UTF-8

# Install apt dependencies
RUN set -x; \
        apt-get install -y --no-install-recommends -qq \
            adduser \
            build-essential \
            libtag1-dev \
            lsb-base \
            mediainfo \
            node-less \
            postgresql \
            postgresql-client \
            python3-babel \
            python3-dateutil \
            python3-decorator \
            python3-dev \
            python3-docutils \
            python3-feedparser \
            python3-gevent \
            python3-html2text \
            python3-jinja2 \
            python3-lxml \
            python3-mako \
            python3-mock \
            python3-passlib \
            python3-pil \
            python3-pip \
            python3-psutil \
            python3-psycopg2 \
            python3-pydot \
            python3-pyldap \
            python3-pyparsing \
            python3-pypdf2 \
            python3-qrcode \
            python3-reportlab \
            python3-requests \
            python3-setuptools \
            python3-suds \
            python3-tz \
            python3-vatnumber \
            python3-vobject \
            python3-werkzeug \
            python3-wheel \
            python3-xlsxwriter \
            python3-yaml \
            wget && \
        apt-get clean -qq

# Install pip dependencies
RUN set -x; \
        pip3 install \
            mutagen==1.40.0 \
            pytaglib==1.4.1

# Install KooZic
RUN set -x; \
        v=v1.1.0 && \
        wget -q https://github.com/DocMarty84/koozic/releases/download/${v}/koozic-${v}.tar.gz && \
        tar xfz koozic-*.tar.gz && \
        rm -rf koozic-*.tar.gz
RUN set -x; \
        tar xfz koozic/extra/ffmpeg/ffmpeg-3.4-64bit.tar.gz -C /usr/local/bin && \
        mv koozic /usr/local/ && \
        chown -R koozic /usr/local/koozic

# Set up PostgreSQL and KooZic
RUN set -x; \
        sed -i 's/5432/54321/g' /etc/postgresql/9.6/main/postgresql.conf && \
        service postgresql start && \
        sleep 5 && \
        su - postgres -c "createuser -s koozic" && \
        su - koozic -c "/usr/local/koozic/odoo-bin -d koozic --db_port=54321 -i oomusic,oovideo --without-demo=all --stop-after-init --log-level=warn"

# Copy entrypoint script
COPY ./entrypoint.sh /

# Expose KooZic services
EXPOSE 8069 8072

ENTRYPOINT ["/entrypoint.sh"]
