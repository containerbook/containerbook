.PHONY: clean

all: examples

unshare:
	$(CC) unshare.c -o unshare

nsenter:
	$(CC) nsenter.c -o nsenter

examples: unshare nsenter

examples_clean:
	rm -f unshare nsenter
