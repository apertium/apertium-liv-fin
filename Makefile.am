###############################################################################
## Makefile for Liv--Finnish
###############################################################################

LANG1=liv
LANG2=fin
PREFIX1=$(LANG1)-$(LANG2)
PREFIX2=$(LANG2)-$(LANG1)

BASENAME=apertium-$(PREFIX1)

TARGETS_DIR1 =		\
	$(PREFIX1).rlx.bin	\
	$(PREFIX1).mor.rlx.bin  \
	$(PREFIX1).syn.rlx.bin  \
	$(PREFIX1).autobil.bin	\
	$(PREFIX1).autolex.bin	\
	$(PREFIX1).autogen.hfst \
	$(PREFIX1).autopgen.bin	\
	$(PREFIX1).t1x.bin	\
	$(PREFIX1).t2x.bin	\
	$(PREFIX1).t3x.bin

#	$(PREFIX1).prob		
TRIMMED_TARGETS_DIR1 = \
	$(PREFIX1).automorf.hfst

UNTRIMMED_TARGETS_DIR1 = \
	$(PREFIX1).automorf-untrimmed.hfst

TARGETS_DIR2 =		\
	$(PREFIX2).rlx.bin	\
	$(PREFIX2).autobil.bin	\
	$(PREFIX2).autolex.bin	\
	$(PREFIX2).autogen.bin	\
	$(PREFIX2).autopgen.bin	\
	$(PREFIX2).t1x.bin	\
	$(PREFIX2).t2x.bin	\
	$(PREFIX2).t3x.bin

#	$(PREFIX2).prob		
TRIMMED_TARGETS_DIR2 = \
	$(PREFIX2).automorf.bin

UNTRIMMED_TARGETS_DIR2 = \
	$(PREFIX2).automorf-untrimmed.bin

TARGETS_COMMON = $(TARGETS_DIR1) $(TRIMMED_TARGETS_DIR1) \
				 $(TARGETS_DIR2) $(TRIMMED_TARGETS_DIR2)

SOURCES = \
	$(BASENAME).$(PREFIX1).t1x			\
	$(BASENAME).$(PREFIX1).t2x			\
	$(BASENAME).$(PREFIX1).t3x			\
	$(BASENAME).$(PREFIX2).t1x			\
	$(BASENAME).$(PREFIX2).t2x			\
	$(BASENAME).$(PREFIX2).t3x			\
	$(BASENAME).$(PREFIX1).dix			\
	$(BASENAME).$(PREFIX1).lrx			\
	$(BASENAME).$(PREFIX2).lrx

liv-fin: $(TARGETS_DIR1) $(TRIMMED_TARGETS_DIR1)
fin-liv: $(TARGETS_DIR2) $(TRIMMED_TARGETS_DIR2)
untrimmed: $(TARGETS_DIR1) $(UNTRIMMED_TARGETS_DIR1) $(TARGETS_DIR2) $(UNTRIMMED_TARGETS_DIR2)

# This include defines goals for install-modes, .deps/.d, autobil.prefixes and .mode files:
@ap_include@

if ENABLE_LINTER
TARGETS_COMMON += lint
endif

.PHONY: lint

lint:
	apertium-lint

###############################################################################
## Monolingual transducers
###############################################################################

# -------------------
# Liv analysis:
# -------------------

.deps/$(LANG1).automorf.hfst: $(AP_SRC1)/apertium-und.$(LANG1)-und.LR.att.gz .deps/.d
	$(ZCAT) $< | hfst-txt2fst > $@

.deps/$(PREFIX1).automorf-trimmed.hfst: .deps/$(LANG1).automorf.hfst .deps/$(PREFIX1).autobil.prefixes
	hfst-compose-intersect -1 $< -2 .deps/$(PREFIX1).autobil.prefixes -o $@

$(PREFIX1).automorf.hfst: .deps/$(PREFIX1).automorf-trimmed.hfst
	hfst-fst2fst -w -i $< -o $@

$(PREFIX1).automorf-untrimmed.hfst: .deps/$(LANG1).automorf.hfst
	hfst-fst2fst -w -i $< -o $@

# -------------------
# Liv generation:
# ---------------------



.deps/$(LANG1).autogen.hfst: $(AP_SRC1)/apertium-$(LANG1).$(LANG1).RL.att.gz .deps/.d
	$(ZCAT) $< | hfst-txt2fst > $@

$(PREFIX2).autogen.hfst: .deps/$(LANG1).autogen.hfst
	hfst-fst2fst -O -i $< -o $@

# -------------------
# Finnish analysis:
# -------------------

$(PREFIX2).automorf-untrimmed.bin: $(AP_SRC2)/$(LANG2).automorf.bin
	cp $< $@

$(PREFIX2).automorf.bin: $(PREFIX2).automorf-untrimmed.bin $(PREFIX2).autobil.bin
	lt-trim $^ $@

# -------------------
# Finnish generation:
# ---------------------



$(PREFIX1).autogen.bin: $(AP_SRC2)/$(LANG2).autogen.bin
	cp $< $@

###############################################################################
## Tagger models
###############################################################################

$(PREFIX1).prob: $(AP_SRC1)/$(LANG1).prob
	cp $< $@

$(PREFIX2).prob: $(AP_SRC2)/$(LANG2).prob
	cp $< $@

###############################################################################
## Disambiguation rules
###############################################################################


$(PREFIX1).mor.rlx.bin: $(AP_SRC1)/$(LANG1).mor.rlx.bin gt2apertium.cg3r
	cg-relabel $^ $@

$(PREFIX1).syn.rlx.bin: $(AP_SRC1)/$(LANG1).syn.rlx.bin gt2apertium.cg3r
	cg-relabel $^ $@


$(PREFIX2).rlx.bin: $(AP_SRC2)/$(LANG2).rlx.bin
	cp $< $@

###############################################################################
## Bilingual dictionaries
###############################################################################

$(PREFIX1).autobil.bin: $(BASENAME).$(PREFIX1).dix
	apertium-validate-dictionary $<
	lt-comp lr $< $@

$(PREFIX2).autobil.bin: $(BASENAME).$(PREFIX1).dix
	apertium-validate-dictionary $<
	lt-comp rl $< $@

###############################################################################
## Lexical selection rules
###############################################################################

$(PREFIX1).autolex.bin: $(BASENAME).$(PREFIX1).lrx
	apertium-validate-lrx $<
	lrx-comp $< $@

$(PREFIX2).autolex.bin: $(BASENAME).$(PREFIX2).lrx
	apertium-validate-lrx $<
	lrx-comp $< $@


###############################################################################
## Structural transfer rules
###############################################################################


$(PREFIX1).t1x.bin: $(BASENAME).$(PREFIX1).t1x
	apertium-validate-transfer $<
	apertium-preprocess-transfer $< $@

$(PREFIX1).t2x.bin: $(BASENAME).$(PREFIX1).t2x
	apertium-validate-interchunk $<
	apertium-preprocess-transfer $< $@

$(PREFIX1).t3x.bin: $(BASENAME).$(PREFIX1).t3x
	apertium-validate-postchunk $<
	apertium-preprocess-transfer $< $@


$(PREFIX2).t1x.bin: $(BASENAME).$(PREFIX2).t1x
	apertium-validate-transfer $<
	apertium-preprocess-transfer $< $@

$(PREFIX2).t2x.bin: $(BASENAME).$(PREFIX2).t2x
	apertium-validate-interchunk $<
	apertium-preprocess-transfer $< $@

$(PREFIX2).t3x.bin: $(BASENAME).$(PREFIX2).t3x
	apertium-validate-postchunk $<
	apertium-preprocess-transfer $< $@

###############################################################################
## Postgeneration dictionaries
###############################################################################

$(PREFIX1).autopgen.bin: $(AP_SRC2)/$(LANG2).autopgen.bin
	cp $< $@

$(PREFIX2).autopgen.bin: $(AP_SRC1)/$(LANG1).autopgen.bin
	cp $< $@

###############################################################################
## Distribution
###############################################################################

EXTRA_DIST=	$(SOURCES) modes.xml

###############################################################################
## Installation
###############################################################################

apertium_liv_findir=$(prefix)/share/apertium/$(BASENAME)/

# Files which should be installed to $(prefix)/share/apertium/$(BASENAME)/:
apertium_liv_fin_DATA= $(TARGETS_COMMON) $(SOURCES)


# All modes are created by the same goal, listing several will just give problems with make -j2
noinst_DATA=modes/$(PREFIX1).mode

install-data-local: install-modes
uninstall-local: uninstall-modes

###############################################################################
## Cleanup
###############################################################################

CLEANFILES = $(TARGETS_COMMON) $(UNTRIMMED_TARGETS_DIR1) $(UNTRIMMED_TARGETS_DIR2)
clean-local:
	-rm -rf .deps modes

###############################################################################
## Test
###############################################################################

test: all
	apertium-regtest test
