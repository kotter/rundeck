include Makefile.inc

DIRS = en jp
ifndef VERSION
VERSION=$(shell grep version.number= ../version.properties | cut -d= -f 2)
endif

ifndef TAG
TAG=$(shell grep version.tag= ../version.properties | cut -d= -f 2)
endif

ifneq ($(TAG),GA)
RVERSION=$(VERSION)-$(TAG)
else
RVERSION=$(VERSION)
endif

.PHONY: all clean

dist/rundeck-docs-$(RVERSION).zip: all
	mkdir -p dist
	for i in $(DIRS) ; do \
		if [ "$$i" != "en" ] ; then \
		mkdir en/dist/html/$$i ; \
		cp -r $$i/dist/html/* en/dist/html/$$i/ ; \
		fi \
	done ;
	cd en/dist && zip -r ../../dist/$(@F) *

all: $(DIRS)
	for i in $^ ; do \
	$(MAKE) VERSION=$(VERSION) TAG=$(TAG) -C $$i ; \
	done ;

clean: $(DIRS)
	for i in $^ ; do \
	$(MAKE) -C $$i clean ; \
	done ;
	rm -rf dist

notes: en/history/toc.conf en/RELEASE.md

en/RELEASE.md: ../RELEASE.md
	cp $< $@
	perl  -i -p -e "s#http://rundeck.org/docs/##" $@

en/history/version-$(RVERSION).md: ../RELEASE.md
	( $(ECHO) "% Version $(RVERSION)" ; \
        $(ECHO) "%" $(shell whoami) ; \
        $(ECHO) "%" $(shell date "+%m/%d/%Y") ; \
        $(ECHO) ; ) >$@
	cat $< >>$@

	perl  -i -p -e "s#http://rundeck.org/docs/#../#" $@

en/history/toc.conf: en/history/version-$(RVERSION).md
	$(ECHO) "1:version-$(RVERSION).md:Version $(RVERSION) ($(shell date "+%Y-%m-%d"))" > $@.new
	test -f $@ && ( grep -v -q "$(RVERSION)" $@ && \
		cat $@ >> $@.new && \
		mv $@.new $@ ) || (  mv $@.new $@ )
