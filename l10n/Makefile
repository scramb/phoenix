# On OSX the PATH variable isn't exported unless "SHELL" is also set, see: http://stackoverflow.com/a/25506676
SHELL = /bin/bash
NODE_BINDIR = ../node_modules/.bin
export PATH := $(NODE_BINDIR):$(PATH)

# Where to find apps (it can be multiple paths).
APPS =  ../apps/files ../apps/pdf-viewer ../apps/markdown-editor

# Where to find core folder
CORE = ../src

# List of all files to extract
INPUT_FILES = $(CORE) $(APPS)

# Where to write the files generated by this makefile.
OUTPUT_DIR = .

# Core Template file
TEMPLATE_FILE = ./template.pot

# Available locales for the app.

LOCALES = de fr es it

# Name of the generated .po files for each available locale.
LOCALE_FILES ?= $(foreach app,$(APPS),$(patsubst %,$(app)/l10n/locale/%/LC_MESSAGES/app.po,$(LOCALES))) $(patsubst %,./locale/%/LC_MESSAGES/app.po,$(LOCALES))

# Makefile Targets
.PHONY: clean extract translations

clean:
	rm -f $(foreach app,$(APPS),$(app)/l10n/template.pot);
	rm -f $(TEMPLATE_FILE);
	rm -rf $(foreach app,$(APPS),$(app)/l10n/locale);
	rm -rf locale;

extract: $(TEMPLATE_FILE)

translations: $(OUTPUT_DIR)/translations.json

# Create a main .pot template, then generate .po files for each available language.
# Thanks to Systematic: https://github.com/Polyconseil/systematic/blob/866d5a/mk/main.mk#L167-L183
$(TEMPLATE_FILE):
# Extract gettext strings from each apps templates files and create a POT dictionary template.
# Generate .po files for each available language.
	@for app in $(INPUT_FILES); \
	do \
		if [ $$app = '../src' ]; then \
			export OUT_DIR=.; \
		else \
			export OUT_DIR=$$app/l10n; \
		fi; \
		mkdir -p $$OUT_DIR; \
		export GETTEXT_APP_SOURCES=`find $$app -name '*.vue'`;\
		node ../node_modules/easygettext/src/extract-cli.js --attribute v-translate --output $$OUT_DIR/template.pot $$GETTEXT_APP_SOURCES; \
	done;

# Generate translations.json file from all available apps .pot templates.
$(OUTPUT_DIR)/translations.json:
	@for app in $(INPUT_FILES); \
	do \
		if [ $$app = '../src' ]; then \
			export OUT_DIR=.; \
		else \
			export OUT_DIR=$$app/l10n; \
		fi; \
		mkdir -p $$OUT_DIR; \
		gettext-compile --output $$OUT_DIR/translations.json $(patsubst %,$$OUT_DIR/locale/%/LC_MESSAGES/app.po,$(LOCALES)); \
	done; \
