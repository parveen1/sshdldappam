# Version: 1.1
# @parveen hisix2 2018-2019
# sshserver
# -------------------------------------
FROM fedora:27
LABEL author="@parveen hisix2"
LABEL description="ssh server "
RUN dnf -y install procps vim passwd openldap-clients nss-pam-ldapd authconfig pam_mount openssh-server nmap
RUN mkdir /opt/docker
COPY * /opt/docker/
RUN chmod +x /opt/docker/install.sh /opt/docker/startup.sh
WORKDIR /opt/docker
EXPOSE 1022
CMD ["/opt/docker/startup.sh"]
