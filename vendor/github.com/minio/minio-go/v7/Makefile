GOPATH := $(shell go env GOPATH)

all: checks

.PHONY: examples docs

checks: lint vet test examples functional-test

lint:
	@mkdir -p ${GOPATH}/bin
	@which golangci-lint 1>/dev/null || (echo "Installing golangci-lint" && curl -sSfL https://raw.githubusercontent.com/golangci/golangci-lint/master/install.sh | sh -s -- -b $(GOPATH)/bin v1.27.0)
	@echo "Running $@ check"
	@GO111MODULE=on ${GOPATH}/bin/golangci-lint cache clean
	@GO111MODULE=on ${GOPATH}/bin/golangci-lint run --timeout=5m --config ./.golangci.yml

vet:
	@GO111MODULE=on go vet ./...

test:
	@GO111MODULE=on SERVER_ENDPOINT=localhost:9000 ACCESS_KEY=minio SECRET_KEY=minio123 ENABLE_HTTPS=1 MINT_MODE=full go test -race -v ./...

examples:
	@mkdir -p /tmp/examples && for i in $(echo examples/s3/*); do go build -o /tmp/examples/$(basename ${i:0:-3}) ${i}; done

functional-test:
	@GO111MODULE=on SERVER_ENDPOINT=localhost:9000 ACCESS_KEY=minio SECRET_KEY=minio123 ENABLE_HTTPS=1 MINT_MODE=full go run functional_tests.go

clean:
	@echo "Cleaning up all the generated files"
	@find . -name '*.test' | xargs rm -fv
	@find . -name '*~' | xargs rm -fv
