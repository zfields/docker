# Container for updating the Notecard firmware

# Build development environment
# $ docker build --tag stm32cubeprog .

# Launch development environment
# (be sure to place Notecard binary in your local ~/Downloads folder)
# $ docker run --interactive --privileged --rm --tty \
#   --volume ~/Downloads/:/root/Downloads/ stm32cubeprog

# Firware update command (example)
# $ STM32_Programmer_CLI --connect port=USB1 --write \
#   /root/Downloads/<notecard-binary.bin> 0x08000000 --verify --go 0x08000000

# NOTE: The Notecard must be in `bootloader direct` mode. This can be
#       accomplished via the Notecard CLI or Developer Web Console.

# Build Argument(s)
ARG STM32CUBE_INSTALLER_ZIP="en.stm32cubeprog_v2-5-0.zip"

# Base Image
FROM ubuntu:18.04

# Build Stage Arguments
ARG DEBIAN_FRONTEND="noninteractive"
ARG STM32CUBE_INSTALLER_ZIP

# Compensate for `openjdk-8-jre-headless` package assumptions
RUN ["dash", "-c", "\
    mkdir -p /usr/share/man/man1/ \
"]

# Build Environment
RUN ["dash", "-c", "\
    apt-get update \
 && apt-get install --assume-yes --no-install-recommends \
      libusb-1.0-0-dev \
      openjdk-8-jre-headless \
      openjfx=8u161-b12-1ubuntu2 \
      libopenjfx-java=8u161-b12-1ubuntu2 \
      libopenjfx-jni=8u161-b12-1ubuntu2 \
      unzip \
 && apt-mark hold \
      openjfx=8u161-b12-1ubuntu2 \
      libopenjfx-java=8u161-b12-1ubuntu2 \
      libopenjfx-jni=8u161-b12-1ubuntu2 \
"]

# Runtime Environment
RUN ["dash", "-c", "\
    update-alternatives --config java \
"]
WORKDIR /root/

# Install/unpack STM32 Cube Installer Scripts
COPY ./${STM32CUBE_INSTALLER_ZIP} .
RUN ["dash", "-c", "\
    unzip ./${STM32CUBE_INSTALLER_ZIP} \
 && rm -rf ./${STM32CUBE_INSTALLER_ZIP} \
"]

ENV PATH=/usr/local/STMicroelectronics/STM32Cube/STM32CubeProgrammer/bin:${PATH}

## HACK #### HACK #### HACK #### HACK ##
## STMCubeProgrammer does not have a headless installer!!!
## 1. Build the image excluding the following folder _(or with an empty folder)_
## 2. Run `~/Setup...linux` script interactively to produce `/usr/local/STMicroelectronics`
## 3. Use `docker cp` to extract the folder
## 4. Rebuild the image including the folder
## HACK #### HACK #### HACK #### HACK ##
COPY ./STMicroelectronics/ /usr/local/STMicroelectronics

ENTRYPOINT ["STM32_Programmer_CLI"]
#CMD ["bash"]
