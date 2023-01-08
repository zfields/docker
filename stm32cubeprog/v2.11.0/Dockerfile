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

# Launch STM32CubeProgrammer GUI
# (be sure to place the binary in your local ~/Downloads folder)
# $ xhost +local:docker
# $ docker run --device /dev/bus/usb --entrypoint STM32CubeProgrammer \
#   --env DISPLAY --net host --rm --tty \
#   --volume ~/Downloads/:/host-volume/ stm32_programmer_cli

# Global Argument(s)
ARG DEBIAN_FRONTEND="noninteractive"

# Base Image
FROM ubuntu:20.04

# Import Global Argument(s)
ARG DEBIAN_FRONTEND

# Local Argument(s)
ARG STM32CUBEPROG_CHECKSUM="a3a9e9fa1f12e1e8334bb4d5742c614f"
ARG STM32CUBEPROG_DIRECTORY="en.stm32cubeprg-lin_v2-11-0"
ARG STM32CUBEPROG_DOWNLOAD_URL="https://www.st.com/content/ccc/resource/technical/software/utility/group0/10/04/c7/eb/76/2e/40/9c/stm32cubeprg-lin_v2-11-0/files/stm32cubeprg-lin_v2-11-0.zip/jcr:content/translations/en.stm32cubeprg-lin_v2-11-0.zip"

# Establish Environment
RUN ["dash", "-c", "\
    apt-get update --quiet \
 && apt-get install --assume-yes --no-install-recommends --quiet \
     ca-certificates \
     curl \
     libcanberra-gtk-module \
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
    curl -SLO# ${STM32CUBEPROG_DOWNLOAD_URL} \
 && md5sum ${STM32CUBEPROG_DIRECTORY}.zip \
 && echo \"${STM32CUBEPROG_CHECKSUM} ${STM32CUBEPROG_DIRECTORY}.zip\" | md5sum -c - \
 && unzip ${STM32CUBEPROG_DIRECTORY}.zip -d ${STM32CUBEPROG_DIRECTORY} \
 && printf \"1\\n1\\n1\\n\\nO\\n1\\nY\\nN\\nN\\n1\\nN\\nN\\n\" | ${STM32CUBEPROG_DIRECTORY}/SetupSTM32CubeProgrammer-2.11.0.linux \
 && rm -rf ${STM32CUBEPROG_DIRECTORY}.zip ${STM32CUBEPROG_DIRECTORY}/ \
"]

ENV PATH=/usr/local/STMicroelectronics/STM32Cube/STM32CubeProgrammer/bin:${PATH}
ENV LD_LIBRARY_PATH=/usr/local/STMicroelectronics/STM32Cube/STM32CubeProgrammer/bin/jre/lib/amd64:/usr/local/STMicroelectronics/STM32Cube/STM32CubeProgrammer/lib:${PATH}

ENTRYPOINT ["STM32_Programmer_CLI"]
