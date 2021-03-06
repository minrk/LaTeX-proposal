###########################################################################
# Automating proposal management by make.
# The participants work on proposal.tex in "draft" mode, which gives a lot
# of information to the developers. Variants submit.tex and public.tex are
# used to prepare official versions (hiding development/private info). 
###########################################################################
# possibly customize the following variables to your setting
PROPOSAL = proposal.tex 	# the proposal
BIB = ../lib/dummy.bib	        # bibTeX databases
PROP.dir = ../../..
###########################################################################
# the following are computed
TARGET = $(PROPOSAL)                         	  # all pdflatex targets
TARGET.pdf 	= $(TARGET:%.tex=%.pdf)         	  # PDFs to be produced
TARGET.aux 	= $(TARGET:%.tex=%.aux)             # their aux files.
PDATA 		= $(PROPOSAL:%.tex=%.pdata)       # the proposal project data
SRC = $(filter-out $(TARGET),$(shell ls *.tex))   # included files
PDFLATEX = pdflatex -interaction batchmode -file-line-error
BBL.base = 1 
BBL = $(PROPOSAL:%.tex=%.bbl) $(BBL.base:%=$(PROPOSAL:%.tex=%)%-blx.bbl)
PROPCLS.dir = $(PROP.dir)/base
PROPETC.dir = $(PROP.dir)/etc
DFGPROPCLS.dir = $(PROP.dir)/dfg
TEXINPUTS := .//:$(PROPCLS.dir)//:$(DFGPROPCLS.dir)//:$(PROPETC.dir)//:
BIBINPUTS := ../lib:$(BIBINPUTS)
PROPCLS.clssty = proposal.cls pdata.sty
PROPETC.sty = workaddress.sty metakeys.sty sref.sty
DFGPROPCLS.clssty = dfgproposal.cls dfgpdata.sty
PROPCLS = $(PROPCLS.clssty:%=$(PROPCLS.dir)/%) $(DFGPROPCLS.clssty:%=$(DFGPROPCLS.dir)/%) $(PROPETC.sty:%=$(PROPETC.dir)/%)

all: $(TARGET.pdf)

submit:
	$(MAKE) -w PROPOSAL=submit.tex all

public:
	$(MAKE) -w PROPOSAL=public.tex all

cd: 	                                           # make cd will prepare CD for burning
	mkdir CD;make $(TARGET.pdf); cp $(TARGET.pdf) CD

bbl: $(BBL)
$(BBL): %.bbl: %.aux
	bibtex -min-crossrefs=100 -terse $<

$(PDATA): %.pdata: %.tex
	$(PDFLATEX) $<

$(TARGET.aux): %.aux: %.tex
	$(PDFLATEX) $<

$(TARGET.pdf): %.pdf: %.tex $(SRC) $(BIB) $(PROPCLS) 
	$(PDFLATEX) $<  || $(RM) $@
#	sort $(PROPOSAL:%.tex=%.delivs) > $(PROPOSAL:%.tex=%.deliverables)
	@if (test -e $(patsubst %.tex, %.idx,  $<));\
	    then makeindex $(patsubst %.tex, %.idx,  $<); fi
	$(MAKE) -$(MAKEFLAGS) $(BBL)
	@if (grep "(re)run BibTeX" $(patsubst %.tex, %.log,  $<)> /dev/null);\
	    then $(MAKE) -B $(BBL); fi
	$(PDFLATEX)  $< || $(RM) $@
	@if (grep Rerun $(patsubst %.tex, %.log,  $<) > /dev/null);\
	   then $(PDFLATEX)  $<  || $(RM) $@; fi
	@if (grep Rerun $(patsubst %.tex, %.log,  $<) > /dev/null);\
	    then $(PDFLATEX)  $<  || $(RM) $@; fi

clean: 
	rm -f *.log *.blg *~ *.synctex.gz *.cut

distclean: clean
	rm -f *.aux *.out *.run.xml *.bbl *.toc *.deliv* *.pdata
	rm -Rf auto

echo:
	echo $(BIBINPUTS)
