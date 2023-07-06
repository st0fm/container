FROM kalilinux/kali-rolling
ARG userid
ARG groupid
ARG username

# user setup
RUN groupadd -g $groupid $username \
 && useradd -m -u $userid -g $groupid -s /usr/bin/zsh $username \
 && echo $username > /root/username
ENV HOME=/home/$username
ENV USER=$username

# install packages
RUN apt update -y \
 && apt install -y mlocate neovim tmux zsh nmap amass sudo htop fzf curl wget socat \
 python3-impacket crackmapexec smbclient mitmproxy ffuf metasploit-framework \
 testssl.sh httpx-toolkit naabu dnsx jadx apktool

# install lf
RUN wget https://github.com/gokcehan/lf/releases/download/r30/lf-linux-amd64.tar.gz \
 && echo -n "8185b7838f84c3f8a9355b76229d24daf155f9001cfd3e3666ead546560d87d7  lf-linux-amd64.tar.gz" | sha256sum -c -  \
 && tar -xf "lf-linux-amd64.tar.gz" -C /usr/local/bin/

# sudo without password
RUN gpasswd -a $username sudo \
 && echo "%sudo ALL=(ALL:ALL) NOPASSWD: ALL" >> /etc/sudoers

# copy configs
COPY configs/zshrc $HOME/.zshrc
COPY configs/tmux.conf $HOME/.tmux.conf
COPY configs/nvim.lua $HOME/.config/nvim/init.lua

ENTRYPOINT chroot --userspec=$(cat /root/username):$(cat /root/username) / \
 /bin/sh -c \
 "sudo chown -R $username:$username /logs && cd ~/ && script -af /logs/log-kali-$(date +%s).log -c '/usr/bin/zsh -i'"
#NTRYPOINT chroot --userspec=$(cat /root/username):$(cat /root/username) / /bin/bash -i
