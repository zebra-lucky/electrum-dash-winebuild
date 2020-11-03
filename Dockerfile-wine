FROM ubuntu:18.04
LABEL maintainer "Andriy Khavryuchenko <akhavr@khavr.com>"

USER root
WORKDIR /root

env WKEY_SHA1 c51bcb8cc4a12abfbd7c7660eaf90f49674d15e222c262f27e6c96429111b822
env WKEY_SHA2 78b185fabdb323971d13bd329fefc8038e08559aa51c4996de18db0639a51df6

RUN dpkg --add-architecture i386 \
    && apt-get update \
    && apt-get install -y --no-install-recommends \
        wget \
        gnupg2=2.2.4-1ubuntu1.3 \
        dirmngr=2.2.4-1ubuntu1.3 \
        python3-software-properties=0.96.24.32.1 \
        software-properties-common=0.96.24.32.1 \
    && wget -nc https://dl.winehq.org/wine-builds/Release.key \
    && echo "${WKEY_SHA1} Release.key" | sha256sum -c - \
    && apt-key add Release.key && rm Release.key \
    && wget -nc https://dl.winehq.org/wine-builds/winehq.key \
    && echo "${WKEY_SHA2} winehq.key" | sha256sum -c - \
    && apt-key add winehq.key && rm winehq.key \
    && apt-add-repository -y https://dl.winehq.org/wine-builds/ubuntu/ \
    && apt-get update \
    && apt-get install -y --no-install-recommends \
        wine-stable-amd64:amd64=4.0.3~bionic \
        wine-stable-i386:i386=4.0.3~bionic \
        wine-stable:amd64=4.0.3~bionic \
        winehq-stable:amd64=4.0.3~bionic \
        cabextract xauth xvfb ca-certificates zip unzip p7zip-full \
    && wget https://raw.githubusercontent.com/Winetricks/winetricks/master/src/winetricks \
    && chmod +x winetricks && mv winetricks /usr/local/bin \
    && rm -rf /var/lib/apt/lists/*

ENV WINEPATH c:/git/cmd;c:/Python37;c:/Python37/Scripts
ENV WINEDEBUG -all
ENV WINEPREFIX /root/.wine-32
ENV WINEARCH win32
ENV PYHOME $WINEPREFIX/drive_c/Python37

ENV PY_PATH https://python.org/ftp/python/3.7.9/
ENV PY32_FILE python-3.7.9.exe
ENV PY32_SHA 769bb7c74ad1df6d7d74071cc16a984ff6182e4016e11b8949b93db487977220
ENV PY64_FILE python-3.7.9-amd64.exe
ENV PY64_SHA e69ed52afb5a722e5c56f6c21d594e85c17cb29f12f18bb69751cf1714e0f987

ENV LIBUSB_PATH https://github.com/libusb/libusb/releases/download/v1.0.23/
ENV LIBUSB_FILE libusb-1.0.23.7z
ENV LIBUSB_SHA d3087e7d09ec4e463f5f4b394dcfec0b90e835545318af1a75575de59d2dfaac

ENV NSIS_FILE nsis-3.05-setup.exe
ENV NSIS_PATH https://downloads.sourceforge.net/project/nsis/NSIS%203/3.05/
ENV NSIS_SHA 1a3cc9401667547b9b9327a177b13485f7c59c2303d4b6183e7bc9e6c8d6bfdb

RUN echo 'download and install 32-bit Python/git/NSIS' \
    && wineboot -i \
    && xvfb-run -a winetricks -q vcrun2015 && xvfb-run -a winetricks win10 \
    \
    && wget -nv ${PY_PATH}${PY32_FILE} \
    && echo "${PY32_SHA} ${PY32_FILE}" | sha256sum -c - \
    && xvfb-run -a wine $PY32_FILE /quiet InstallAllUsers=1 \
        TargetDir=$PYHOME && rm $PY32_FILE \
    && wine python -m pip install -U pip \
    \
    && wget -nv ${LIBUSB_PATH}${LIBUSB_FILE} \
    && echo "${LIBUSB_SHA} ${LIBUSB_FILE}" | sha256sum -c - \
    && 7z x -olibusb $LIBUSB_FILE -aos && rm $LIBUSB_FILE  \
    && cp libusb/MS32/dll/libusb-1.0.dll $PYHOME/ \
    \
    && wget -nv ${NSIS_PATH}${NSIS_FILE} \
    && echo "${NSIS_SHA} ${NSIS_FILE}" | sha256sum -c - \
    && wine $NSIS_FILE /S \
    \
    && rm -rf /tmp/.wine-0


ENV WINEPREFIX /root/.wine-64
ENV WINEARCH win64
ENV PYHOME $WINEPREFIX/drive_c/Python37

RUN echo 'download and install 64-bit Python/git/NSIS' \
    && wineboot -i && xvfb-run -a winetricks win10 \
    \
    && wget -nv ${PY_PATH}${PY64_FILE} \
    && echo "${PY64_SHA} ${PY64_FILE}" | sha256sum -c - \
    && xvfb-run -a wine $PY64_FILE /quiet InstallAllUsers=1 \
        TargetDir=$PYHOME && rm $PY64_FILE \
    && wine python -m pip install -U pip \
    \
    && cp libusb/MS64/dll/libusb-1.0.dll $PYHOME/ && rm -rf libusb \
    \
    && wine $NSIS_FILE /S  && rm $NSIS_FILE \
    \
    && rm -rf /tmp/.wine-0
