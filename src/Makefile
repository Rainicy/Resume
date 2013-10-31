# ------------------------------------------------------------------------
# Makefile: automatic paper generation using LaTeX, dvips, etc.
# ------------------------------------------------------------------------
# written by:   Rainer Doemer (and others)
# last update:  Dec 18, 2006
#
# modifications:
# 06/19/06 RD   added LaTeX source files (SOURCES)
# 12/28/09 GS   auto collection of *.tex and FIGURES/*.eps files
#
#
# --- macros -------------------------------------------------------------

LATEX	=	TEXINPUTS=.:MACROS: latex
BIBTEX	=	bibtex
# removed -Z flag, it caused table lines to be fat
#  also some lines in graphs became fat ...
DVIPS	=	dvips -D 600 -t letter -Ppdf
FIGPS	=	fig2dev -L ps
PSSEL	=	psselect
TAR	=	gtar cvzf
RM	=	rm -f
MV	=	mv
CAT	=	cat
GZIP	=	gzip -f
SPELL	=	nspell -t
SORT	=	sort -f
MAKEIDX	=	makeindex
DISTILL =       GS_OPTIONS="-dAutoFilterColorImages=false \
                            -dColorImageFilter=/FlateEncode" ps2pdf14
THUMBPDF=       thumbpdf --modes=dvips

# base name of the root LaTeX file 
PROJECT	=	resume

TARGETS	=	$(PROJECT).pdf 

THUMBS	=	$(PROJECT).tpm

# auto use all *.tex file in the own directory as dependency input 
SOURCES	:=	$(filter-out $(PROJECT).tex, $(shell echo *.tex ))

MODULES =	

FIGURES =	

# auto use all *.eps files in the FIGURES directory as a dependency input 
#  FIGURES directory may not exist
PSFIGURES :=	$(shell [ -d FIGURES ] && echo FIGURES/*.eps) 

INCLUDES =

LISTINGS =

MACROS =        

OTHERS	=	Makefile

# --- general rules ------------------------------------------------------


.SUFFIXES:
.SUFFIXES:	.fig .prn .eps .tex .ind .idx .bbl .dvi .ps .pdf .tpm

%.eps: 	%.fig
	$(FIGPS) $*.fig $*.eps

%.eps: 	%.prn
	$(PRN2EPS) $*.prn $*.eps


%.bbl:	%.tex $(MODULES) $(PSFIGURES) $(INCLUDES) $(MACROS)
	$(LATEX) $*.tex
	#$(BIBTEX) $*

%.idx %.inx:	%.tex $(MODULES) $(PSFIGURES) $(INCLUDES) $(MACROS)
	$(LATEX) $*.tex
	$(LATEX) $*.tex

%.ind:	%.idx
	$(MAKEIDX) $*

%.ps:	%.dvi $(PSFIGURES)
	$(DVIPS) $*.dvi -o $*.ps

%.pdf:	%.ps
	$(DISTILL) $*.ps

%.tpm:	%.pdf
	$(THUMBPDF) $*.pdf


# --- rules --------------------------------------------------------------


all:	$(TARGETS)
	cp $(TARGETS) ../pdf/

final:	$(TARGETS) $(THUMBS)
	set -e;  for t in $(THUMBS); do			\
	  BASE=`basename $$t .tpm` ; 			\
	  $(LATEX) $$BASE.tex ;				\
	  $(DVIPS) $$BASE.dvi -o $$BASE.ps ;		\
	  $(DISTILL) $$BASE.ps ;			\
	done

new:		clean all

clean:
	$(RM) *.dvi *.ps *.ps.gz *.pdf *.tpm
	$(RM) *.toc *.lot *.lof *.lop *.log *.aux *.blg *.bbl
	$(RM) *.idx *.ind *.ilg
	$(RM) *.inx *.srt
	$(RM) *.BAK *.bak *~
	$(RM) texput.log
	$(RM) $(PROJECT).out

chmod:
	chgrp -fR specc *
	chmod -fR u+rwX,g-w+rX,o-rwx *

spell:
	$(SPELL) $(MODULES)

tar:
	$(RM) book.inx ; touch book.inx
	$(TAR) $(PROJECT).tar.gz $(SOURCES) $(MODULES) $(INCLUDES) \
		$(FIGURES) $(PSFIGURES) $(LISTINGS) $(MACROS) $(OTHERS)

$(PROJECT).dvi:	$(PROJECT).tex $(PROJECT).bbl $(MODULES) $(PSFIGURES) $(INCLUDES) $(MACROS) $(SOURCES)
	$(LATEX) $(PROJECT).tex
	$(LATEX) $(PROJECT).tex


draft.dvi:	draft.tex draft.bbl $(MODULES) $(PSFIGURES) $(INCLUDES) $(MACROS)
	$(LATEX) draft.tex
	$(LATEX) draft.tex

report.dvi:	report.tex $(PROJECT).bbl $(MODULES) $(PSFIGURES) $(INCLUDES) $(MACROS)
	$(LATEX) $(PROJECT).tex
	$(LATEX) $(PROJECT).tex

book.dvi:	book.tex book.srt book.bbl $(MODULES) $(PSFIGURES) $(INCLUDES) $(MACROS)
	$(LATEX) book.tex
	$(LATEX) book.tex

book.srt:	book.inx
	$(SORT) book.inx -o book.srt

chapter.ps:	book.ps
	$(PSSEL) -p3- book.ps chapter.ps


# debug rule to print dynamically determined values
echo:
	echo "SOURCES" $(SOURCES)
	echo "PSFIGURES" $(PSFIGURES)


# --- EOF ----------------------------------------------------------------
