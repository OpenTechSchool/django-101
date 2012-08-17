# MakeFile for building all the docs at once.
# Inspired by the Makefile used by bazaar. 
# http://bazaar.launchpad.net/~bzr-pqm/bzr/2.3/
 
PYTHON = python
BUILDDIR = ../build
 
.PHONY: all clean html dirhtml singlehtml

help:
	@echo "Please use \`make <target>' where <target> is one of"
	@echo "  html       to make standalone HTML files"
	@echo "  dirhtml    to make HTML files named index.html in directories"
	@echo "  singlehtml to make a single large HTML file"

# Dependencies to perform before running other builds.
SPHINX_DEPENDENCIES = \
	en/Makefile

# Copy-paste the english Makefile everwhere its needed.
%/Makefile : en/Makefile
	$(PYTHON) -c "import shutil; shutil.copyfile('$<', '$@')"
 
# Make the HTML version of the documentation with correctly nested language folders.
html: $(SPHINX_DEPENDENCIES)
	cd en && make html LANG=en BUILDDIR=${BUILDDIR}
	cd de && make html LANG=de BUILDDIR=${BUILDDIR}

dirhtml: $(SPHINX_DEPENDENCIES)
	cd en && make dirhtml LANG=en BUILDDIR=${BUILDDIR}
	cd de && make dirhtml LANG=de BUILDDIR=${BUILDDIR}

singlehtml: $(SPHINX_DEPENDENCIES)
	cd en && make singlehtml LANG=en BUILDDIR=${BUILDDIR}
	cd de && make singlehtml LANG=de BUILDDIR=${BUILDDIR}
 
clean:
	rm -rf build
