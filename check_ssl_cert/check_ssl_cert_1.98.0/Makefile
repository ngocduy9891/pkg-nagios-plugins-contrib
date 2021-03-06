PLUGIN=check_ssl_cert
VERSION=`cat VERSION`
DIST_DIR=$(PLUGIN)-$(VERSION)
DIST_FILES=AUTHORS COPYING ChangeLog INSTALL Makefile NEWS README.md TODO VERSION $(PLUGIN) $(PLUGIN).spec COPYRIGHT ${PLUGIN}.1 test
YEAR=`date +"%Y"`

dist: version_check formatting_check
	rm -rf $(DIST_DIR) $(DIST_DIR).tar.gz
	mkdir $(DIST_DIR)
	cp -r $(DIST_FILES) $(DIST_DIR)
	tar cfz $(DIST_DIR).tar.gz  $(DIST_DIR)
	tar cfj $(DIST_DIR).tar.bz2 $(DIST_DIR)

install:
	mkdir -p $(DESTDIR)
	install -m 755 $(PLUGIN) $(DESTDIR)
	mkdir -p ${MANDIR}/man1
	install -m 644 ${PLUGIN}.1 ${MANDIR}/man1/

version_check:
	grep --quiet "VERSION\ *=\ *[\'\"]*$(VERSION)" $(PLUGIN)
	grep --quiet "^%define\ version\ *$(VERSION)" $(PLUGIN).spec
	grep --quiet -- "- $(VERSION)-" $(PLUGIN).spec
	grep --quiet "\"$(VERSION)\"" $(PLUGIN).1
	grep --quiet "${VERSION}" NEWS
	echo "Version check: OK"

formatting_check:
	grep --invert-match --quiet '\\t' check_ssl_cert test/unit_tests.sh

clean:
	rm -f *~
	rm -rf rpmroot

distclean: clean
	rm -rf check_ssl_cert-[0-9]*
	rm -f *.crt
	rm -f *.error

test: dist
	( export SHUNIT2="$$(pwd)/shunit2/shunit2" && cd test && ./unit_tests.sh )

shellcheck:
	if shellcheck --help 2>&1 | grep --quiet -- '-o\ ' ; then shellcheck -o all check_ssl_cert test/unit_tests.sh ; else shellcheck check_ssl_cert test/unit_tests.sh ; fi

copyright_check:
	grep --quiet "(c) Matteo Corti, 2007-$(YEAR)" README.md
	grep --quiet "Copyright (c) 2007-$(YEAR) Matteo Corti" COPYRIGHT
	grep --quiet "Copyright (c) 2007-$(YEAR) Matteo Corti <matteo@corti.li>" $(PLUGIN)
	echo "Copyright year check: OK"

rpm: dist
	mkdir -p rpmroot/SOURCES rpmroot/BUILD
	cp $(DIST_DIR).tar.gz rpmroot/SOURCES
	rpmbuild --define "_topdir `pwd`/rpmroot" -ba check_ssl_cert.spec



.PHONY: install clean test rpm distclean
