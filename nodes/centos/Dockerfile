FROM centos:7

# Install packages
RUN yum install -y openssh-server openssh-clients vim
# Modify /etc/ssh/sshd_config file 
RUN sed -i 's/#PermitRootLogin yes/PermitRootLogin yes/g' /etc/ssh/sshd_config && \
sed -i 's/#PubkeyAuthentication yes/PubkeyAuthentication yes/g' /etc/ssh/sshd_config

# Create missing SSH default keys
RUN ssh-keygen -A && \
echo 'root:root' | chpasswd


# Starting SSH daemon
CMD ["/usr/sbin/sshd", "-f", "/etc/ssh/sshd_config", "-D", "-e"]