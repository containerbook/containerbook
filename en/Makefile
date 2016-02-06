SUBDIRS := $(shell find . -name code-samples -type d)

.PHONY: force

all: $(SUBDIRS)

examples:
	for dir in $(SUBDIRS); do \
	    $(MAKE) -C $$dir examples; \
	done

examples_clean:
	for dir in $(SUBDIRS); do \
	    $(MAKE) -C $$dir examples_clean; \
	done
