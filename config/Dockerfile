# based on the Adobe Install guide at https://helpx.adobe.com/coldfusion/pdf-generation-in-coldfusion.html
# with silent-install options on pdfg from Immanuel Noel via CFML Slack on 5//4/17 (the silent.properties file passed into the .bin installer)
# custom Jetty XML and start.ini by Samuel Knowlton to remove host whitelist and allow connections on 0.0.0.0 since we want our other containers to be able to access it

FROM ubuntu:bionic

LABEL version="@version@"
LABEL maintainer "Samuel Knowlton <sam@inleague.org>"
LABEL repository "inleague/coldfusion_pdfg:acf11"

# ACF 11 PDFG runs on 8987; other versions use other ports

EXPOSE 8987

# 1) install openJDK 8
# 2) Fix certificate issues, found as of 
# https://bugs.launchpad.net/ubuntu/+source/ca-certificates-java/+bug/983302
# 3) add packages required by Adobe PDFG

RUN dpkg --add-architecture i386 && \
	apt-get update && \
	apt-get install -y tzdata && \
	apt-get install -y openjdk-8-jdk && \
	apt-get install -y ant && \
	rm -rf /var/cache/oracle-jdk8-installer && \
	apt-get install -y ca-certificates-java && \
	update-ca-certificates -f && \
	rm -rf /var/cache/oracle-jdk8-installer && \
	apt-get install -y libc6:i386 libncurses5:i386 libstdc++6:i386 lib32z1 lib32ncurses5 libbz2-1.0:i386 lib32z1-dev libbz2-dev:i386 zlib1g libx11* lib32z1 lib32ncurses5 && \
	apt-get install -y libbz2-1.0:i386 libexpat1 libexpat1:i386 libfreetype6:i386 x-window-* libnss-mdns:i386 libxcb1-dev:i386 libxcb1-dev libxext6 libxext6:i386 libsm6 && \
	apt-get install -y libsm6:i386 libxrandr2 libxrandr2:i386 libxrender1 libxrender1:i386 libxinerama1 libxinerama1:i386 expat curl && \
	apt-get clean && \
	rm -rf /var/lib/apt/lists/*;

# install add'l font per adobe install guide
ADD ./archives/font-ibm-type1-1.0.3.tar.gz /root/build 

RUN mkdir -p /usr/share/fonts && \
	mv /root/build/font-ibm-type1-1.0.3/* /usr/share/fonts && \
	rm -rf /root/build/font-ibm-type1-1.0.3

ENV JAVA_HOME /usr/lib/jvm/java-8-openjdk-amd64/

COPY ./build/ /root/build

RUN export JAVA_HOME && \
	/root/build/coldfusion_11_addon_linux64.bin LAX_VM ${JAVA_HOME}/bin/java -i silent -f /root/build/silent.properties  && \
   rm /root/build/coldfusion_11_addon_linux64.bin && \
   mv /root/build/jetty.xml /opt/pdfg/etc \
   && mv /root/build/jetty-ipaccess.xml /opt/pdfg/etc/extra \
   && mv /root/build/start.ini /opt/pdfg 

WORKDIR /opt/pdfg

CMD ["java","-jar","./start.jar"]