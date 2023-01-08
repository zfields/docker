# Container for updating STM32 firmware

# Build development environment
# $ docker build --file Dockerfile.stm32cubeprog --tag stm32_programmer_cli .

# Launch development environment
# (be sure to place the binary in your local ~/Downloads folder)
# $ docker run --device /dev/bus/usb --rm --tty \
#   --volume ~/Downloads/:/host-volume/ stm32_programmer_cli \
#   --connect port=USB1 \
#   --write /host-volume/<stm32-binary.bin> 0x08000000 \
#   --verify \
#   --go

# Launch STM32CubeProgrammer GUI (Does not work - known issue in v2.8.0)
# (be sure to place the binary in your local ~/Downloads folder)
# $ xhost +local:docker
# $ docker run --device /dev/bus/usb --entrypoint STM32CubeProgrammer \
#   --env DISPLAY --net host --rm --tty \
#   --volume ~/Downloads/:/host-volume/ stm32_programmer_cli

# Global Argument(s)
ARG DEBIAN_FRONTEND="noninteractive"

# Base Image
FROM ubuntu:18.04

# Import Global Argument(s)
ARG DEBIAN_FRONTEND

# Local Argument(s)
ARG STM32CUBEPROG_CHECKSUM="c6849f274b6dfd0819a541e4b4082755"

# Environment Variables
ENV LIBGL_ALWAYS_INDIRECT=1
ENV NO_AT_BRIDGE=1

# Establish Environment
RUN ["dash", "-c", "\
    apt-get update --quiet \
 && apt-get install --assume-yes --no-install-recommends --quiet \
     ca-certificates \
     curl \
     libcanberra-gtk-module \
     libcanberra-gtk3-module \
     libglib2.0-0 \
     libopenjfx-jni \
     libusb-1.0-0 \
     libx11-6 \
     unzip \
 && apt-get clean \
 && apt-get purge \
 && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* \
"]

WORKDIR /host-volume

# Download/Install STM32CubeProg Utilities
RUN ["dash", "-c", "\
    curl -SLO# https://www.st.com/content/ccc/resource/technical/software/utility/group0/0a/02/5f/ff/fb/27/4d/84/stm32cubeprg-lin_v2-8-0/files/stm32cubeprg-lin_v2-8-0.zip/jcr:content/translations/en.stm32cubeprg-lin_v2-8-0.zip \
 && md5sum en.stm32cubeprg-lin_v2-8-0.zip \
 && echo \"${STM32CUBEPROG_CHECKSUM} en.stm32cubeprg-lin_v2-8-0.zip\" | md5sum -c - \
 && unzip en.stm32cubeprg-lin_v2-8-0.zip -d STM32CubeProgrammer-2.8.0 \
 && printf \"1\\n1\\n1\\n\\nO\\n1\\nY\\nN\\nN\\n1\\nN\\nN\\n\" | STM32CubeProgrammer-2.8.0/SetupSTM32CubeProgrammer-2.8.0.linux \
 && rm -rf en.stm32cubeprg-lin_v2-8-0.zip STM32CubeProgrammer-2.8.0 \
"]

ENV PATH=/usr/local/STMicroelectronics/STM32Cube/STM32CubeProgrammer/bin:${PATH}
ENV LD_LIBRARY_PATH=/usr/local/STMicroelectronics/STM32Cube/STM32CubeProgrammer/bin/jre/lib/amd64:/usr/local/STMicroelectronics/STM32Cube/STM32CubeProgrammer/lib:${PATH}

ENTRYPOINT ["STM32_Programmer_CLI"]
