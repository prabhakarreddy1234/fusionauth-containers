#
# FusionAuth App Dockerfile
#
# Build:
#   > cp ~/dev/inversoft/fusionauth/fusionauth-app/build/bundles/*.zip .
#   > docker build -t fusionauth/fusionauth-app:dev .
#
# Run:
#  > docker run -p 9011:9011 -it fusionauth/fusionauth-app
#

FROM debian:stretch-slim

LABEL description="Create an image running FusionAuth App. Installs FusionAuth App"
MAINTAINER FusionAuth <dev@fusionauth.io>

EXPOSE 9011

###### Install stuff we need and then cleanup cache #################
RUN apt-get update && apt-get install -y --no-install-recommends \
 unzip \
 wget \
 curl \
 && rm -rf /var/lib/apt/lists/* \
 && groupadd -g 1000 fusionauth \
 && useradd -g fusionauth -u 1000 fusionauth \
 && mkdir -p /usr/local/fusionauth \
 && chown fusionauth:fusionauth /usr/local/fusionauth

###### Install Java #################################################
RUN mkdir -p /usr/local/fusionauth/java \
 && curl -Sk --progress-bar https://storage.googleapis.com/inversoft_products_j098230498/java/jre/jre-linux-1.8.0+u171.tar.gz -o jre-linux-1.8.0+u171.tar.gz \
 && tar xfz jre-linux-1.8.0+u171.tar.gz -C /usr/local/fusionauth/java \
 && ln -s /usr/local/fusionauth/java/jre1.8.0_171 /usr/local/fusionauth/java/current \
 && rm jre-linux-1.8.0+u171.tar.gz \
 && chown -R fusionauth:fusionauth /usr/local/fusionauth/java

###### Copy the latest dev build ####################################
COPY fusionauth-app-*zip fusionauth-app.zip

###### Install FusionAuth App Bundle ########################
RUN mkdir -p /usr/local/fusionauth/fusionauth-app \
  && unzip -nq fusionauth-app.zip -d /usr/local/fusionauth \
  && rm fusionauth-app.zip \
  && find /usr/local/fusionauth -type d -maxdepth 1 -not -user fusionauth -not -group fusionauth -exec chown -R fusionauth:fusionauth {} +

###### Start FusionAuth App #########################################
USER fusionauth
CMD /usr/local/fusionauth/fusionauth-app/apache-tomcat/bin/catalina.sh run