ARG BASE_IMAGE=jcbird1012/neko:base
FROM $BASE_IMAGE

ARG SRC_URL="https://github.com/macchrome/linchrome/releases/download/v90.0.4430.85-r857950-portable-ungoogled-Lin64/ungoogled-chromium_90.0.4430.85_3.vaapi_linux.tar.xz"

#
# install custom chromium build from woolyss with support for hevc/x265
RUN set -eux; apt-get update; \
    apt-get install -y --no-install-recommends wget unzip libatk1.0-0 libatk-bridge2.0-0 libatomic1 \
    libcups2 libgtk-3-0 libnss3 libpci3 libxcomposite1 libxss1 openbox xz-utils; \
    wget -O - /tmp/chromium.tar.xz "${SRC_URL}" | tar -xJf- -C /tmp; \
    mv /tmp/ungoogled-chromium_* /usr/lib/chromium; \
    #
    # make required changes for sandbox mode
    mv /usr/lib/chromium/chrome_sandbox /usr/lib/chromium/chrome-sandbox; \
    chown root:root /usr/lib/chromium/chrome-sandbox; \
    chmod 4755 /usr/lib/chromium/chrome-sandbox; \
    #
    # install widevine module
    WIDEVINE_VERSION=$(wget --quiet -O - https://dl.google.com/widevine-cdm/versions.txt | tail -n 1); \
    wget -O /tmp/widevine.zip "https://dl.google.com/widevine-cdm/$WIDEVINE_VERSION-linux-x64.zip"; \
    unzip -p /tmp/widevine.zip libwidevinecdm.so > /usr/lib/chromium/libwidevinecdm.so; \
    chmod 644 /usr/lib/chromium/libwidevinecdm.so; \
    rm /tmp/widevine.zip; \
    #
    # clean up
    apt-get --purge autoremove -y xz-utils; \
    apt-get clean -y; \
    rm -rf /var/lib/apt/lists/* /var/cache/apt/*

#
# copy configuation files
COPY supervisord.conf /etc/neko/supervisord/ungoogled-chromium.conf
COPY preferences.json /usr/lib/chromium/master_preferences
COPY policies.json /etc/chromium/policies/managed/policies.json
COPY openbox.xml /etc/neko/openbox.xml

#
# copy extensions and policy files
COPY extensions /usr/share/chromium/extensions
