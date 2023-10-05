NAME=activedirectory
BINARY=terraform-provider-${NAME}
OS_ARCH=linux_amd64
VERSION=0.7.0
HOST=registry.terraform.io
NAMESPACE=hashicorp
ROOT_DIR:=$(shell dirname $(realpath $(firstword $(MAKEFILE_LIST))))
GO_CACHE= GOCACHE=$(ROOT_DIR)/.gocache
TEST?=$$( $(GO_CACHE) go  list ./... | grep -v 'vendor')

ifneq ("$(wildcard ./testacc.env)","")
	include testacc.env
	export $(shell sed 's/=.*//' testacc.env)
endif

default: install

vendor:
	$(GO_CACHE) go mod vendor

build: vendor
	$(GO_CACHE) go build -o ${BINARY}

release:
	$(GO_CACHE) goreleaser release

install: build
	mkdir -p ~/.terraform.d/plugins/${HOST}/${NAMESPACE}/${NAME}/${VERSION}/${OS_ARCH}
	mv ${BINARY} ~/.terraform.d/plugins/${HOST}/${NAMESPACE}/${NAME}/${VERSION}/${OS_ARCH}

test:
	$(GO_CACHE) go test -i $(TEST) || exit 1
	echo $(TEST) | xargs -t -n4 go test $(TESTARGS) -timeout=30s -parallel=4

testacc: vendor
	TF_ACC=1 \
	$(GO_CACHE) go test -coverprofile=coverage.out $(TEST) -v $(TESTARGS) -timeout 120m
