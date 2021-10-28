FROM debian:buster-slim as base

RUN addgroup --gid 1000 mainsail
RUN useradd -rm -d /home/mainsail -u 1000 -g 1000 mainsail

RUN apt-get update
RUN apt-get install -y sudo wget cmake swig git supervisor

RUN echo '%mainsail ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

FROM base as stack

USER mainsail

WORKDIR /tmp
RUN wget https://github.com/Klipper3d/klipper/raw/master/scripts/install-debian.sh
RUN head -n -5 install-debian.sh > install-debian2.sh
RUN wget https://raw.githubusercontent.com/Arksine/moonraker/master/scripts/install-moonraker.sh
RUN head -n -7 install-moonraker.sh > install-moonraker2.sh

RUN /bin/bash -c "source /tmp/install-debian2.sh && install_packages"
RUN /bin/bash -c "source /tmp/install-moonraker2.sh && install_packages"

FROM stack as config

USER root

COPY supervisor/supervisord.conf /etc/supervisor/supervisord.conf

RUN usermod -aG dialout,tty,sudo mainsail
RUN apt-get install -y systemd
RUN mkdir -p /run/systemd && echo "docker" > /run/systemd/container
RUN apt-get -y autoremove \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

COPY start-api /bin/start-api
RUN chmod +x /bin/start-api

USER mainsail

WORKDIR /home/mainsail

EXPOSE 7125

ENTRYPOINT ["/bin/start-api"]