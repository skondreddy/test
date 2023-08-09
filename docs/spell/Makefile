#
# SESCO documentation spelling dictionary
#

BUILD_DICT_FLAGS  = --lang=en create master ./sesco.dict

BUILD_DICT        = aspell $(BUILD_DICT_FLAGS)

all: sesco.dict 

sesco.dict: words
	$(BUILD_DICT) < words

dist-clean:
	rm -f .DS_Store > /dev/null

maint-clean: dist-clean
	rm -f sesco.dict > /dev/null 2>&1

.PHONY: all dist-clean maint-clean
