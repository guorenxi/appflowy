FROM archlinux/archlinux:base-devel

RUN pacman -Syy 

RUN pacman -Syu --needed --noconfirm git xdg-user-dirs

# makepkg user and workdir
ARG user=makepkg
ENV PATH="/home/makepkg/.local/flutter/bin:/home/makepkg/.local/flutter/bin/cache/dart-sdk/bin:${PATH}"
RUN useradd --system --create-home $user \
  && echo "$user ALL=(ALL:ALL) NOPASSWD:ALL" > /etc/sudoers.d/$user
USER $user
WORKDIR /home/$user

# Install yay
RUN git clone https://aur.archlinux.org/yay.git \
  && cd yay \
  && makepkg -sri --needed --noconfirm \
  && cd \
  && rm -rf .cache yay 

RUN yay -S --noconfirm curl base-devel sqlite openssl clang cmake ninja pkg-config gtk3 unzip
RUN xdg-user-dirs-update
RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
RUN source $HOME/.cargo/env && rustup toolchain install nightly && rustup default nightly
RUN git clone https://github.com/flutter/flutter.git $HOME/.local/flutter
RUN flutter channel stable
RUN flutter config --enable-linux-desktop 
RUN flutter doctor 

RUN git clone https://github.com/AppFlowy-IO/appflowy.git && \
cd appflowy/frontend && \
source $HOME/.cargo/env && \
cargo install --force cargo-make && \
cargo install --force duckscript_cli && \
cargo make flowy_dev && \
cargo make -p production-linux-x86 appflowy-linux
CMD ["appflowy/frontend/app_flowy/product/0.0.2/linux/Release/AppFlowy/app_flowy"]
