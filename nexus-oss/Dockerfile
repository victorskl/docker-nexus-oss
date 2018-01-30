FROM sonatype/nexus3

# temporary switch to root for setting TZ
USER root

# Set the timezone - CentOS
ENV TZ=Australia/Melbourne
#RUN timedatectl set-timezone $TZ
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime

# now back to nexus user
USER nexus