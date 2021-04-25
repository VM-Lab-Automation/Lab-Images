FROM ubuntu:16.04

#setup user
RUN useradd -ms /bin/bash p4
RUN echo "p4:p4" | chpasswd

#setup ssh
RUN apt-get update && apt-get install -y openssh-server
RUN mkdir /var/run/sshd
# SSH login fix. Otherwise user is kicked off after login
RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd
RUN echo "PermitRootLogin yes" >> /etc/ssh/sshd_config

ENV NOTVISIBLE "in users profile"
RUN echo "export VISIBLE=now" >> /etc/profile
EXPOSE 22

#setup gui and xrdp
ENV DEBIAN_FRONTEND noninteractive
WORKDIR /scripts
COPY bootstrap_gui.sh ./
RUN chmod +x *.sh
RUN ./bootstrap_gui.sh
RUN apt-get update && apt-get install -y supervisor locales
ADD etc /etc


RUN sed -i '/TerminalServerUsers/d' /etc/xrdp/sesman.ini && \
    sed -i '/TerminalServerAdmins/d' /etc/xrdp/sesman.ini

RUN echo "xfce4-session" > /etc/skel/.Xclients

CMD ["supervisord", "-n"]

RUN apt-get update && apt-get install -y gedit evince sudo

RUN echo "root:root" | chpasswd

COPY root-bootstrap.sh ./
COPY user-bootstrap.sh ./
RUN chmod +x *.sh
RUN ./root-bootstrap.sh
RUN ./user-bootstrap.sh