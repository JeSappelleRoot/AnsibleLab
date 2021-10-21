ARG VERSION_FEDORA

FROM fedora:$VERSION_FEDORA

# Install packages
RUN yum install -y openssh-server openssh-clients python
# Modify /etc/ssh/sshd_config file 
RUN sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/g' /etc/ssh/sshd_config && \
sed -i 's/#PubkeyAuthentication yes/PubkeyAuthentication yes/g' /etc/ssh/sshd_config

# Create missing SSH default keys
RUN ssh-keygen -A && \
echo 'root:root' | chpasswd


# Starting SSH daemon
CMD ["/usr/sbin/sshd", "-f", "/etc/ssh/sshd_config", "-D", "-e"]