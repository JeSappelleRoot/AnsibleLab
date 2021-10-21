FROM ubuntu:20.04

# Install packages
RUN apt-get update && apt-get install -y ssh python
# Modify /etc/ssh/sshd_config file 
RUN sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/g' /etc/ssh/sshd_config && \
sed -i 's/#PubkeyAuthentication yes/PubkeyAuthentication yes/g' /etc/ssh/sshd_config

# Create missing SSH default keys
# Update root password
# and create /run/sshd missing folder
RUN ssh-keygen -A && \
echo 'root:root' | chpasswd && \
mkdir /run/sshd

# Starting SSH daemon
CMD ["/usr/sbin/sshd", "-f", "/etc/ssh/sshd_config", "-D", "-e"]