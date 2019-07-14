FROM tomcat:9-jdk11-corretto AS tomcat

FROM buzzxu/java:11

LABEL MAINTAINER buzzxu <downloadxu@163.com>

ENV CATALINA_HOME /usr/local/tomcat
ENV PATH $CATALINA_HOME/bin:$PATH
# let "Tomcat Native" live somewhere isolated
ENV TOMCAT_NATIVE_LIBDIR $CATALINA_HOME/native-jni-lib
ENV LD_LIBRARY_PATH ${LD_LIBRARY_PATH:+$LD_LIBRARY_PATH:}$TOMCAT_NATIVE_LIBDIR

COPY --from=tomcat $CATALINA_HOME $CATALINA_HOME

WORKDIR $CATALINA_HOME

RUN apk update && apk upgrade ; \
	apk add --no-cache -U apr-dev openssl openssl-dev bash apr; \
	find ./bin/ -name '*.sh' -exec sed -ri 's|^#!/bin/sh$|#!/usr/bin/env bash|' '{}' +; \
	chmod -R +rX .; \
	chmod 777 logs work ; \
	rm -rf /var/cache/apk/* ; \
    rm -rf /tmp/* 

# verify Tomcat Native is working properly
RUN set -e \
	&& nativeLines="$(catalina.sh configtest 2>&1)" \
	&& nativeLines="$(echo "$nativeLines" | grep 'Apache Tomcat Native')" \
	&& nativeLines="$(echo "$nativeLines" | sort -u)" \
	&& if ! echo "$nativeLines" | grep 'INFO: Loaded APR based Apache Tomcat Native library' >&2; then \
		echo >&2 "$nativeLines"; \
		exit 1; \
	fi
	
EXPOSE 8080
CMD ["catalina.sh", "run"]