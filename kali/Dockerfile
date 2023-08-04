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
 && apt install -y tmux zsh nmap amass sudo htop fzf curl wget socat \
 python3-impacket python3.11-venv crackmapexec smbclient mitmproxy ffuf metasploit-framework \
 testssl.sh naabu dnsx jadx apktool jq rsync nuclei gawk chromium iproute2 whois

# install lf
RUN wget https://github.com/gokcehan/lf/releases/download/r30/lf-linux-amd64.tar.gz \
 && echo -n "8185b7838f84c3f8a9355b76229d24daf155f9001cfd3e3666ead546560d87d7  lf-linux-amd64.tar.gz" | sha256sum -c -  \
 && tar -xf "lf-linux-amd64.tar.gz" -C /usr/local/bin/

# install nvim (version in kali repo is to old)
RUN wget https://github.com/neovim/neovim/releases/download/v0.9.1/nvim-linux64.tar.gz \
 && echo -n "6c083017304213c3a3efde8d332a52231b8df8206d35146942097c303ebf93d5  nvim-linux64.tar.gz" | sha256sum -c - \
 && tar -xf "nvim-linux64.tar.gz" -C /tmp/ \
 && rsync -a /tmp/nvim-linux64/ /usr/local/

# install httpx
RUN wget https://github.com/projectdiscovery/httpx/releases/download/v1.3.4/httpx_1.3.4_linux_amd64.zip \
  && echo -n "5560e5da12fd66d4215981d9f73c3e10bfd0180d3b3440133e76c2a7bb95a891  httpx_1.3.4_linux_amd64.zip" | sha256sum -c - \
  && unzip httpx_*_linux_amd64.zip -d /tmp/ \
  && cp /tmp/httpx /usr/local/bin/

# install aquatone
RUN wget https://github.com/michenriksen/aquatone/releases/download/v1.7.0/aquatone_linux_amd64_1.7.0.zip \
  && echo -n "077119d9ccc5e19bb7323a65402aeb4f3460297fb8107efaeeb428e0982fdeac  aquatone_linux_amd64_1.7.0.zip" | sha256sum -c - \
  && unzip aquatone_linux_amd64*.zip -d /tmp/ \
  && cp /tmp/aquatone /usr/local/bin/


# sudo without password
RUN gpasswd -a $username sudo \
 && echo "%sudo ALL=(ALL:ALL) NOPASSWD: ALL" >> /etc/sudoers

# copy configs
COPY --chown=$username:$username configs/zshrc $HOME/.zshrc
COPY --chown=$username:$username configs/tmux.conf $HOME/.tmux.conf
COPY --chown=$username:$username configs/nvim.lua $HOME/.config/nvim/init.lua

ENTRYPOINT chroot --userspec=$(cat /root/username):$(cat /root/username) / \
 /bin/sh -c \
 "sudo chown -R $username:$username /logs && cd ~/ && script -af /logs/log-kali-$(date +%s).log -c '/usr/bin/zsh -i'"
#NTRYPOINT chroot --userspec=$(cat /root/username):$(cat /root/username) / /bin/bash -i
