FROM  jenkins/jenkins:2.346.3-2-lts-jdk11
USER root
RUN apt-get update \
	&& DEBIAN_FRONTEND=noninteractive \
	apt-get install --no-install-recommends --assume-yes \
	docker.io
RUN curl https://get.docker.com/ > dockerinstall && chmod 777 dockerinstall && ./dockerinstall
USER jenkins