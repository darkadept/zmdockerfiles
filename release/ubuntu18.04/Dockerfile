FROM ubuntu:18.04
MAINTAINER Peter Gallagher

# Update base packages
RUN apt update \
    && apt upgrade --assume-yes

# Install pre-reqs
RUN apt install --assume-yes --no-install-recommends gnupg cron nano

# Configure Zoneminder PPA
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys ABE4C7F993453843F0AEB8154D0BF748776FFB04 \
    && echo deb http://ppa.launchpad.net/iconnor/zoneminder-1.36/ubuntu bionic main > /etc/apt/sources.list.d/zoneminder.list \
    && apt update

# Install zoneminder
RUN DEBIAN_FRONTEND=noninteractive apt install --assume-yes zoneminder \
    && a2enconf zoneminder \
    && a2enmod rewrite cgi

# Setup Volumes
VOLUME /var/cache/zoneminder/events /var/cache/zoneminder/images /var/lib/mysql /var/log/zm

# Expose http port
EXPOSE 80

# Configure entrypoint
COPY utils/entrypoint.sh /usr/local/bin/
COPY utils/zmu_killer.sh /usr/local/bin/
RUN chmod 755 /usr/local/bin/entrypoint.sh
RUN chmod 755 /usr/local/bin/zmu_killer.sh

RUN crontab -l | { cat; echo "0 */3 * * * /usr/local/bin/zmu_killer.sh"; } | crontab -

ENTRYPOINT ["/usr/local/bin/entrypoint.sh"]
