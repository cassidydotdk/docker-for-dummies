ARG BASE_IMAGE

FROM $BASE_IMAGE as builder

RUN apt-get -y update \
    && apt-get -y install unzip \ 
    && wget -progress=bar:force -q -O sqlpackage.zip https://go.microsoft.com/fwlink/?linkid=2087431 \
    && unzip -qq sqlpackage.zip -d /opt/sqlpackage \
    && chmod +x /opt/sqlpackage/sqlpackage

COPY *.zip /opt/wdp/

RUN unzip -qq /opt/wdp/*.scwdp.zip -d /opt/wdp/

ENV DB_PREFIX='sc'

RUN mkdir -p /install \
    && chmod -R 700 /install \
    && cp /clean/* /install/ \
    && ( /opt/mssql/bin/sqlservr & ) | grep -q "Service Broker manager has started" \
    && ./opt/attach-databases.sh /install \
    && chmod +x /opt/install-databases.sh && ./opt/install-databases.sh /opt/wdp \
    && pkill sqlservr

FROM $BASE_IMAGE

COPY --from=builder ["/install/*", "/clean/"]
