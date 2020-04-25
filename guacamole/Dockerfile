FROM guacamole/guacamole

RUN  \
 sed -i "/\/Host/i \        <Valve className=\"org.apache.catalina.valves.RemoteIpValve\" \
 \              internalProxies=\"172.16.17.1\" \
 \              remoteIpHeader=\"x-forwarded-for\" \
 \              remoteIpProxiesHeader=\"x-forwarded-by\" \
 \              protocolHeader=\"x-forwarded-proto\" />" /usr/local/tomcat/conf/server.xml

