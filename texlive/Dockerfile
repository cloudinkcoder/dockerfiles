FROM debian:latest
LABEL maintainer="Aaron Chan <zwchen037@gmail.com>"

ENV LANG=C.UTF-8 \
  LC_ALL=C.UTF-8 \
  DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
  wget perl fontconfig xz-utils make python3 \
  && apt-get clean && rm -rf /var/lib/apt/lists/*

WORKDIR /tex
COPY texlive.profile .

ARG TL_VERSION=2026 ARCH_OS=x86_64-linux
# Office source repo url: http://ctan.net
# Auto Mirror: http://mirror.ctan.org
# ========== Other mirror ==========
# Aliyun texlive mirror: https://mirrors.aliyun.com/CTAN
ARG TL_URL=http://mirror.ctan.org

# Download and Install TeXLive
RUN wget -q ${TL_URL}/systems/texlive/tlnet/install-tl-unx.tar.gz \
  && tar -xzf install-tl-unx.tar.gz && rm install-tl-unx.tar.gz \
  && ./install-tl-*/install-tl --profile=texlive.profile --repository=${TL_URL}/systems/texlive/tlnet > install.log \
  && rm -rf install-tl-* install.log

# Add path to environment
ENV PATH="/usr/local/texlive/${TL_VERSION}/bin/${ARCH_OS}:$PATH"

# Updated TeXLive package manage and Install fonts package
RUN tlmgr update --self --all \
  && tlmgr install tex-gyre droid cm-unicode roboto junicode lm qualitype arphic-ttf fandol libertine \
  && luaotfload-tool --update \
  && cp "$(find /usr/local/texlive -name texlive-fontconfig.conf)" /etc/fonts/conf.d/09-texlive-fonts.conf \
  && fc-cache -fsv \
  && tex -v && latex -v && bibtex -v && xetex -v && pdftex -v && luatex -v && latexmk -v \
  && tlmgr --version && texconfig --version
