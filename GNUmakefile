help: ## print this help
	@grep -E '^[a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | sort | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-30s\033[0m %s\n", $$1, $$2}'

TEST?=$$(go list ./... |grep -v 'vendor')
GOFMT_FILES?=$$(find . -name '*.go' |grep -v vendor)

default: build ## default job

build: fmtcheck ## build the binary
	go install

test: fmtcheck ## run the test suite
	go test -i $(TEST) || exit 1
	echo $(TEST) | \
		xargs -t -n4 go test $(TESTARGS) -timeout=30s -parallel=4

testacc: fmtcheck ## run testacc suite
	TF_ACC=1 go test $(TEST) -v $(TESTARGS) -timeout 120m

vet: ## running go vet
	@echo "go vet ."
	@go vet $$(go list ./... | grep -v vendor/) ; if [ $$? -eq 1 ]; then \
		echo ""; \
		echo "Vet found suspicious constructs. Please check the reported constructs"; \
		echo "and fix them if necessary before submitting the code for review."; \
		exit 1; \
	fi

fmt: ## run go format
	gofmt -w $(GOFMT_FILES)

fmtcheck: ## run gofmtcheck.sh
	@sh -c "'$(CURDIR)/scripts/gofmtcheck.sh'"

errcheck: ## run errcheck
	@sh -c "'$(CURDIR)/scripts/errcheck.sh'"

vendor-status: ## run vendor-status
	@govendor status

test-compile: ## run the test-compile
	@if [ "$(TEST)" = "./..." ]; then \
		echo "ERROR: Set TEST to a specific package. For example,"; \
		echo "  make test-compile TEST=./aws"; \
		exit 1; \
	fi
	go test -c $(TEST) $(TESTARGS)

.PHONY: build test testacc vet fmt fmtcheck errcheck vendor-status test-compile
