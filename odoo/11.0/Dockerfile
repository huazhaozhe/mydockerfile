FROM ubuntu:16.04
MAINTAINER zhe <1655604975@qq.com>

# 环境
RUN set -x; \
        apt-get update \
        && apt-get install -y --no-install-recommends \
        locales \
        fontconfig \
        fonts-wqy-zenhei \
        fonts-wqy-microhei \
        curl \
        wget \
        vim \
        python \
        python-pip \
        python3 \
        python3-pip \
        python-setuptools \
        python3-setuptools \
        tree
        
RUN pip install wheel -i https://pypi.tuna.tsinghua.edu.cn/simple
RUN pip3 install wheel -i https://pypi.tuna.tsinghua.edu.cn/simple

# 时区
COPY Shanghai /etc/localtime
RUN echo 'Asia/Shanghai' > /etc/timezone

# 中文
RUN locale-gen zh_CN.UTF-8 && \
  DEBIAN_FRONTEND=noninteractive dpkg-reconfigure locales
RUN locale-gen zh_CN.UTF-8  
ENV LANG zh_CN.UTF-8  
ENV LANGUAGE zh_CN:zh  
ENV LC_ALL zh_CN.UTF-8




# Generate locale C.UTF-8 for postgres and general locale data
# ENV LANG C.UTF-8

# Install some deps, lessc and less-plugin-clean-css, and wkhtmltopdf
RUN set -x; \
        apt-get update \
        && apt-get install -y --no-install-recommends \
            ca-certificates \
            node-less \
            python3-renderpm \
            libssl-dev \
            xz-utils \
            libxrender1 \
            libxext6 \
            libfontconfig1 \
        && curl -o wkhtmltox.tar.xz -SL https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.4/wkhtmltox-0.12.4_linux-generic-amd64.tar.xz \
        && echo '3f923f425d345940089e44c1466f6408b9619562 wkhtmltox.tar.xz' | sha1sum -c - \
        && tar xvf wkhtmltox.tar.xz \
        && cp wkhtmltox/lib/* /usr/local/lib/ \
        && cp wkhtmltox/bin/* /usr/local/bin/ \
        && cp -r wkhtmltox/share/man/man1 /usr/local/share/man/

# Install Odoo
ENV ODOO_VERSION 11.0
ENV ODOO_RELEASE 20180122
RUN set -x; \
        curl -o odoo.deb -SL http://nightly.odoo.com/${ODOO_VERSION}/nightly/deb/odoo_${ODOO_VERSION}.${ODOO_RELEASE}_all.deb \
        && echo '56f61789bc655aaa2c014a3c5f63d80805408359 odoo.deb' | sha1sum -c - \
        && dpkg --force-depends -i odoo.deb \
        && apt-get update \
        && apt-get -y install -f --no-install-recommends \
        && rm -rf /var/lib/apt/lists/* odoo.deb

# Copy entrypoint script and Odoo configuration file
RUN pip3 install num2words xlwt wheel -i https://pypi.tuna.tsinghua.edu.cn/simple
COPY ./requirements.txt /etc/odoo/
RUN pip3 install -r /etc/odoo/requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple
COPY ./entrypoint.sh /
COPY ./odoo.conf /etc/odoo/
RUN chown odoo /etc/odoo/odoo.conf

# Mount /var/lib/odoo to allow restoring filestore and /mnt/extra-addons for users addons
RUN mkdir -p /mnt/extra-addons \
        && chown -R odoo /mnt/extra-addons
VOLUME ["/var/lib/odoo", "/mnt/extra-addons"]

# Expose Odoo services
EXPOSE 8069 8071

# Set the default config file
ENV ODOO_RC /etc/odoo/odoo.conf

# Set default user when running the container
USER odoo

ENTRYPOINT ["/entrypoint.sh"]
CMD ["odoo"]
