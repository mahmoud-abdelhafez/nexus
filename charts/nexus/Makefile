CHART_REPO := http://jenkins-x-chartmuseum:8080
NAME := nexus
OS := $(shell uname)
VERSION := $(shell cat ../../version/VERSION)

build: clean
	helm dependency build
	helm lint

install: clean build
	helm install . --name ${NAME}
	watch kubectl get pods

upgrade: clean build
	helm upgrade ${NAME} .
	watch kubectl get pods

delete:
	helm delete --purge ${NAME}

clean:
	rm -rf charts
	rm -rf ${NAME}*.tgz
	rm -rf requirements.lock

release: clean build
ifeq ($(OS),Darwin)
	sed -i "" -e "s/version:.*/version: $(VERSION)/" Chart.yaml
	sed -i "" -e "s/tag:.*/tag: $(VERSION)/" values.yaml

else ifeq ($(OS),Linux)
	sed -i -e "s/version:.*/version: $(VERSION)/" Chart.yaml
	sed -i -e "s/tag:.*/tag: $(VERSION)/" values.yaml
else
	exit -1
endif
	helm package .
	curl --fail -u $(CHARTMUSEUM_CREDS_USR):$(CHARTMUSEUM_CREDS_PSW) --data-binary "@$(NAME)-$(VERSION).tgz" $(CHART_REPO)/api/charts
	rm -rf ${NAME}*.tgz