FROM alpine:latest
COPY main /
WORKDIR /
RUN apk add --update bash openrc openssh ca-certificates && \
    mkdir -p ~root/.ssh && chmod 700 ~root/.ssh/ && \
    echo -e "Port 22\n" >> /etc/ssh/sshd_config && \
    cp -a /etc/ssh /etc/ssh.cache && \
    rm -rf /var/cache/apk/*
EXPOSE 22
CMD ["/usr/sbin/sshd", "-D", "-f", "/etc/ssh/sshd_config"]
