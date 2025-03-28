FROM ubuntu:20.04

ARG  MOSQUITTOVERSION
ENV  MOSQUITTOVERSION 2.0.11

MAINTAINER Ansgar Schmidt <ansgar.schmidt@gmx.net>

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update     && \
    apt-get upgrade -y && \
    apt-get install -y net-tools curl mosquitto-clients && \
    apt-get install -y wget build-essential libwrap0-dev libssl-dev python-distutils-extra \
                       libc-ares-dev uuid-dev libwebsockets-dev
RUN apt-get -y install cmake 
RUN apt-get install -y zip

RUN     mkdir -p /usr/local/src
WORKDIR /usr/local/src

RUN     wget https://github.com/DaveGamble/cJSON/archive/refs/heads/master.zip
RUN     unzip ./master.zip
WORKDIR /usr/local/src/cJSON-master
RUN     cmake .
RUN     make && make install

WORKDIR /usr/local/src
RUN     wget http://mosquitto.org/files/source/mosquitto-2.0.11.tar.gz
RUN     tar xvzf ./mosquitto-2.0.11.tar.gz

WORKDIR /usr/local/src/mosquitto-2.0.11
RUN   sed -i 's/WITH_WEBSOCKETS:=no/WITH_WEBSOCKETS:=yes/g' config.mk

RUN     make && make install
RUN     ldconfig 

RUN     touch /etc/mosquitto/mosquitto.conf
RUN     echo "listener 1883 0.0.0.0" >> /etc/mosquitto/mosquitto.conf
RUN     echo "allow_anonymous true" >> /etc/mosquitto/mosquitto.conf

RUN     echo "listener 9001 0.0.0.0" >> /etc/mosquitto/mosquitto.conf
RUN     echo "protocol websockets" >> /etc/mosquitto/mosquitto.conf
RUN     echo "allow_anonymous true" >> /etc/mosquitto/mosquitto.conf

RUN     adduser --system --disabled-password --disabled-login mosquitto
USER    mosquitto

EXPOSE  1883
EXPOSE  9001

CMD     /usr/local/sbin/mosquitto -c /etc/mosquitto/mosquitto.conf -v
