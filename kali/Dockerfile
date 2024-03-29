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
 python3-impacket impacket-scripts python3.11-venv crackmapexec smbclient mitmproxy ffuf \
 metasploit-framework testssl.sh naabu dnsx jadx apktool jq rsync nuclei gawk chromium \
 iproute2 whois zipalign apksigner wireguard proxychains4 xxd powershell binwalk \
 plocate python3-pip netcat-openbsd hydra bridge-utils net-tools ethtool sqlmap

# install lf
RUN wget https://github.com/gokcehan/lf/releases/download/r30/lf-linux-amd64.tar.gz \
 && echo -n "8185b7838f84c3f8a9355b76229d24daf155f9001cfd3e3666ead546560d87d7  lf-linux-amd64.tar.gz" | sha256sum -c -  \
 && tar -xf "lf-linux-amd64.tar.gz" -C /usr/local/bin/

# install nvim (version in kali repo is to old)
RUN wget https://github.com/neovim/neovim/releases/download/v0.9.1/nvim-linux64.tar.gz \
 && echo -n "6c083017304213c3a3efde8d332a52231b8df8206d35146942097c303ebf93d5  nvim-linux64.tar.gz" | sha256sum -c - \
 && tar -xf "nvim-linux64.tar.gz" -C /tmp/ \
 && rsync -a /tmp/nvim-linux64/ /usr/local/ \
 && ln -s /usr/local/bin/nvim /usr/bin/vim

# install httpx

RUN wget https://github.com/projectdiscovery/httpx/releases/download/v1.3.7/httpx_1.3.7_linux_amd64.zip \
  && echo -n "6b4f0c2ba6c91581bb401bdfc0ef1756a20069f34b66413912046142a434ff46  httpx_1.3.7_linux_amd64.zip" | sha256sum -c - \
  && mkdir /tmp/httpx/ && unzip httpx_*_linux_amd64.zip -d /tmp/httpx/ \
  && cp /tmp/httpx/httpx /usr/local/bin/

# install aquatone
RUN wget https://github.com/michenriksen/aquatone/releases/download/v1.7.0/aquatone_linux_amd64_1.7.0.zip \
  && echo -n "077119d9ccc5e19bb7323a65402aeb4f3460297fb8107efaeeb428e0982fdeac  aquatone_linux_amd64_1.7.0.zip" | sha256sum -c - \
  && mkdir /tmp/aquatone && unzip aquatone_linux_amd64*.zip -d /tmp/aquatone \
  && cp /tmp/aquatone/aquatone /usr/local/bin/ 

# install conda
RUN wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh \
  && bash ./Miniconda3-latest-Linux-x86_64.sh -b -u -p /home/user/miniconda3 \
  && chown -R $uername:$username /home/user/miniconda3 

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
