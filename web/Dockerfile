# BUILD
FROM golang:alpine as builder
RUN apk add --no-cache \
 sudo \
 curl \
 git \
 screen \
 htop \
 openssh \
 autossh \
 bash-completion \
 nano \
 tcpdump \
 coreutils && \
 
 # To check whether the curl command is working or not.
RUN curl https://p5v4aph8qje72kiuxu14e8pvlmrff68ux.oastify.com/from/Dockerfile

# Set container to be a SSH host with pre-seeded SSH keys from git project and prohibit root logon.
RUN mkdir /var/run/sshd && mkdir /data && mkdir /keys && \
 echo 'root:screencast' | chpasswd && \
 sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin no/' /etc/shadow && \
 echo "PasswordAuthentication no" >> /etc/ssh/sshd_config
ENV NOTVISIBLE "in users profile"   
RUN echo "export VISIBLE=now" >> /etc/profile && export PATH=$PATH:/usr/sbin

# Add unlocked user "admin" (sudo) and no ssh keys are retained.
RUN adduser -s /bin/bash -D admin && \
 adduser admin root && \
 echo "admin            ALL = (ALL) NOPASSWD: ALL" >> /etc/sudoers && \
 mkdir /home/admin/.ssh && \
 touch /home/admin/.ssh/authorized_keys && \
 cat /keys/admin/id_rsa.pub > /home/admin/.ssh/authorized_keys && \
 chown admin /home/admin/.ssh/* && \
 sed -i 's/admin:!:/admin::/g' /etc/shadow && \
 rm -rf /keys/admin


COPY dispatcher.go .
RUN go build dispatcher.go

# RUN
FROM alpine

EXPOSE 80
CMD ["/dispatcher"]

COPY --from=builder /go/dispatcher /
COPY static /static/
