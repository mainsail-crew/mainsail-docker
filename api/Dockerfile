FROM python:3-buster

RUN addgroup --gid 1000 mainsail
RUN useradd -rm -d /home/mainsail -u 1000 -g 1000 mainsail

RUN apt-get update
RUN apt-get install -y sudo wget cmake swig git supervisor

RUN echo '%mainsail ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

USER mainsail

WORKDIR /tmp
RUN wget https://github.com/Klipper3d/klipper/raw/master/scripts/install-debian.sh
RUN bash /tmp/install-debian.sh 2>/dev/null; exit 0;

USER root

COPY start-api /bin/start-api
RUN chmod +x /bin/start-api

USER mainsail
WORKDIR /home/mainsail

ENTRYPOINT ["/bin/start-api"]