SHELL := /bin/bash

.PHONY: run

install:
	python3 -m venv venv && \
	source ./venv/bin/activate && \
	pip3 install mkdocs && \
	pip3 install mkdocs-techdocs-core

build:
	source ./venv/bin/activate && \
	mkdocs build

run:
	source ./venv/bin/activate && \
	mkdocs serve