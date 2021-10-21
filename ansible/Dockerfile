ARG VERSION_DEBIAN_ANSIBLE


FROM actions/ssh-keygen as keygeneration

RUN mkdir -p /artifacts/keys /run/sshd && \
ssh-keygen -t ed25519 -N '' -f /artifacts/keys/ansible_key -C root && \
cat /artifacts/keys/ansible_key.pub > /artifacts/keys/authorized_keys

FROM debian:$VERSION_DEBIAN_ANSIBLE

# Install initial packages
RUN apt-get update && apt-get install -y \
ansible \
ansible-doc \
ansible-lint \
vim \
ssh \
sshpass

# Add the private key in ansible config file
# and update root password
RUN sed -i 's+#private_key_file = /path/to/file+private_key_file = /etc/ansible/SSH/ansible_key+g' /etc/ansible/ansible.cfg && \
echo 'root:root' | chpasswd && \ 
mkdir /run/sshd
# Get SSH keys created from 1st stage
COPY --from=keygeneration /artifacts/keys/* /etc/ansible/SSH/

# Start SSH daemon to listen incoming connections
CMD ["/usr/sbin/sshd", "-f", "/etc/ssh/sshd_config", "-D", "-e"]
