# For this example we take an Ubuntu image as basis.
FROM ubuntu:16.04

# This is optional. There are many other LABELs possible.
LABEL maintainer "thomas.schwade@zerodrive.net"

# We need Java to run the Software AG installer. 
# This is only used during the installation.
# The Natural installation brings its own Java with it.
RUN apt-get update && \
    apt-get install -y openjdk-8-jdk

# Create the user sag under which we will run the Natural installation.
RUN useradd -ms /bin/bash sag && \
    echo 'sag:sag' | chpasswd && \
    mkdir /opt/softwareag && \
    chown -R sag:sag /opt/softwareag

# Provide the NWO port to the external world.
EXPOSE 2900

# We need these files for the Natural installation.
USER sag
WORKDIR /home/sag
# The installer.
COPY SoftwareAGInstaller20170503.jar /home/sag
# The installation image.
COPY nat_nwo_8.3.8.zip /home/sag
# The installation script.
COPY nat_nwo_8.3.8 /home/sag 
# The license file.
# Make sure not only that your license file matches the platform,
# but also that your usage is within the license terms.
COPY 0000298294_nat83.xml /home/sag

# Now run the Natural installation.
RUN java -jar SoftwareAGInstaller20170503.jar -readScript nat_nwo_8.3.8 -readImage nat_nwo_8.3.8.zip -console

# Run the post-installation script.
USER root
RUN /opt/softwareag/bin/afterInstallAsRoot.sh

# Exchange the default fuser with the one that contains our application.
COPY fuser /opt/softwareag/Natural/fuser
RUN chown -R sag:sag /opt/softwareag/Natural/fuser
RUN chmod 777 /opt/softwareag/Natural/nwo/ce4e6f5a3526/nwo.sh

# Everything up to this point is run when the container is built.
# The following command is run when the container is executed.

# Set the Natural environment and start the NWO server.
USER sag
CMD . /opt/softwareag/Natural/INSTALL/natenv && \
    /opt/softwareag/Natural/bin/natstart.bsh && \
    /opt/softwareag/Natural/INSTALL/sag1nwosrvd start && \
    sleep infinity
