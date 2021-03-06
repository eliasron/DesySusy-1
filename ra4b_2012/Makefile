#Set Makefile for the DesySusy ntuple analysis
#
# Author: Dirk Kruecker, 10.2.2011

#----------------------- here you can input your files and libraries ----------
#
# we compile all *.C and *.cpp defined in SOURCES
#
  VPATH = ./src
  SRCDIR   = ./src/
  SOURCES  = main_RA4b.cpp Nminus1Tree.cpp defaultTree.cpp trigStudyTree.cpp makeJets.cpp makeMuons.cpp makeElectrons.cpp vertices_RA4b.cpp triggers_RA4b.cpp triggers_RA4b_frequency.cpp evtqual_RA4b.cpp metAndHT_RA4b.cpp SetTriggers_RA4b.cpp SetConditions_RA4b.cpp ConfigReader.cpp CutFlow.cpp cschalo_RA4b.cpp trackingFailure_RA4b.cpp AnalysisObject.cpp Particle.cpp Electron.cpp Muon.cpp Jet.cpp eventselection.cpp HistoMaker.cpp mt2w_bisect.cpp mt2w_interface.cpp systematics_RA4b.cpp makeSystematics.cpp anDiLep.cpp TagEff.cpp makeOsLepPairs.cpp JetMonitor.cpp genJet.cpp TriggerEfficiencyProvider.cpp

# and create this executable
#
  EXEC=myAnalysis

# with additional libs and includes
# e.g. MORELIBS = -L/path -lblub
  MORELIBS =
# e.g. -I/path
  MOREINCS = -I$(TOOLSDIR)

OBJS      = $(notdir $(SOURCES))
#OBJS     = $(SOURCES)
OBJS     := $(OBJS:.C=.o)
OBJS     := $(OBJS:.cpp=.o)
#$(info Using the following files: $(SOURCES) )
#$(info Using the following files: $(OBJS) )

#Define source for main_2
SOURCES2  = main_2.cpp Event.cpp Nminus1Tree.cpp defaultTree.cpp makeJets.cpp makeMuons.cpp makeElectrons.cpp vertices_RA4b.cpp triggers_RA4b.cpp triggers_RA4b_frequency.cpp evtqual_RA4b.cpp metAndHT_RA4b.cpp SetTriggers_RA4b.cpp SetConditions_RA4b.cpp ConfigReader.cpp CutFlow.cpp cschalo_RA4b.cpp trackingFailure_RA4b.cpp AnalysisObject.cpp Particle.cpp Electron.cpp Muon.cpp Jet.cpp eventselection.cpp HistoMaker.cpp mt2w_bisect.cpp mt2w_interface.cpp anDiLep.cpp TagEff.cpp makeOsLepPairs.cpp trigStudyTree.cpp JetMonitor.cpp genJet.cpp
OBJS2    = $(notdir $(SOURCES2))
OBJS2   := $(OBJS2:.C=.o)
OBJS2   := $(OBJS2:.cpp=.o)

#Define sources for test. Leaves out some files for simplicity.
SOURCES_TEST  = Nminus1Tree.cpp defaultTree.cpp trigStudyTree.cpp makeJets.cpp makeMuons.cpp makeElectrons.cpp vertices_RA4b.cpp triggers_RA4b.cpp triggers_RA4b_frequency.cpp evtqual_RA4b.cpp metAndHT_RA4b.cpp SetTriggers_RA4b.cpp SetConditions_RA4b.cpp ConfigReader.cpp CutFlow.cpp cschalo_RA4b.cpp trackingFailure_RA4b.cpp AnalysisObject.cpp Particle.cpp Electron.cpp Muon.cpp Jet.cpp eventselection.cpp HistoMaker.cpp mt2w_bisect.cpp mt2w_interface.cpp anDiLep.cpp TagEff.cpp makeOsLepPairs.cpp
OBJS_TEST      = $(notdir $(SOURCES_TEST))
OBJS_TEST     := $(OBJS_TEST:.cpp=.o)	 

#Define sources to be used with a shared library
SOURCES_LIB  = AnalysisObject.cpp Particle.cpp Electron.cpp Muon.cpp Jet.cpp mt2w_bisect.cpp mt2w_interface.cpp anDiLep.cpp
HEADERS_LIB = $(notdir $(SOURCES_LIB))
HEADERS_LIB:= $(HEADERS_LIB:.cpp=.h)	
OBJS_LIB   = $(notdir $(SOURCES_LIB))
OBJS_LIB   := $(OBJS_LIB:.cpp=_sl.o)	 

SOURCES_MTTOOLS = ./src/mTtools/MT2/mt2_bisect.cpp 
OBJS_MTTOOLS   := $(SOURCES_MTTOOLS:.cpp=.o)	 
OBJS_MTTOOLS_LIB   := $(SOURCES_MTTOOLS:.cpp=_sl.o)	 

#------------------------------------------------------------------------------

ifndef ROOTSYS
	$(error ROOTSYS is not defined!)
endif

#GET THE BOOST DIR. REQUIRED ON WORKGROUP SERVER
BOOSTLIB = /usr/include
ifneq "$(shell find $(BOOSTLIB) -type d -iname boost | wc -l)"  "1"
$(info "Setting BOOST dir...")
BOOSTLIB = /afs/desy.de/group/cms/sw/slc5_ia32_gcc434/external/boost/1.44.0-cms/include
endif
$(info "Using BOOST: $(BOOSTLIB)")

ROOTLIBS    = `root-config --libs` -lGenVector
CXX         = g++ -g3 -O1
CXXFLAGS    = `root-config --cflags` -I ./  -I ./src -I ./src/CMSSW_5_3_3_patch2/src -I$(BOOSTLIB)
LD          = g++
LDFLAGS     = `root-config --ldflags`

.SUFFIXES: .cpp .C .o .so

# do we have a 32-bit root?
BUILDTYPE = $(findstring 64-bit, $(shell file -L ${ROOTSYS}/lib/libCore.so ) )
# check if we have changed the environment (32/64) and recompile everything
FILETYPE =  $(findstring 64-bit, $(shell file -L NtupleTools2Dict.o ) )
ifneq (${BUILDTYPE},${FILETYPE}) 
$(info Different build-types for ROOT and NtupleTools2Dict. Recompiling all.)
$(shell rm *.o NtupleTools2Dict.cxx )
endif



#------------------------------------------------------------------------------

all: MTTOOLS $(EXEC)

$(EXEC): $(OBJS) NtupleTools2Dict.o 
	$(LD) $(LDFLAGS) $(OBJS) NtupleTools2Dict.o $(OBJS_MTTOOLS) $(ROOTLIBS) $(MORELIBS)  -o $@
	@echo "$@ done"
	@echo "$(OBJS) 	$(LD) $(LDFLAGS) $(OBJS) NtupleTools2Dict.o $(OBJS_MTTOOLS) $(ROOTLIBS) $(MORELIBS)  -o $@"

NtupleTools2Dict.cxx: NtupleTools2_h.h  Linkdef.h
		@echo "Generating dictionary $@..."
		@rootcint -f $@ -c  -p $^
NtupleTools2Dict.o: NtupleTools2Dict.cxx
	 $(CXX) $(CXXFLAGS) -D __NTHEADER___ -c $<	
NtupleTools2_h.h:  NtupleTools2.h
	touch NtupleTools2_h.h 

MTTOOLS:
	@echo "Making MTTOOLS..."
	cd src/mTtools/MT2; make
	cd src/mTtools/WIMPMASS; make

clean:
	@rm -f myAnalysis *Dict.* *Dict_* *.so core $(OBJS) $(OBJS_LIB) *.d 

.cpp.o:
		$(CXX) $(CXXFLAGS) -c $<
		$(info .cpp.p rule got called!)
.C.o:
		$(CXX) $(CXXFLAGS) -c $<
ConfigReader.o : ConfigReader.cpp
		$(CXX) $(CXXFLAGS) -c $<
CutFlow.o : CutFlow.cpp
		$(CXX) $(CXXFLAGS) -c $<

#Rules for creating main_2
main2: $(OBJS2) NtupleTools2Dict.o 
	$(LD) $(LDFLAGS) $(OBJS2) NtupleTools2Dict.o $(OBJS_MTTOOLS) $(ROOTLIBS) $(MORELIBS)  -o $(EXEC)
	@echo "$@ done"
	@echo "$(OBJS) 	$(LD) $(LDFLAGS) $(OBJS2) NtupleTools2Dict.o $(OBJS_MTTOOLS) $(ROOTLIBS) $(MORELIBS)  -o $(EXEC)"


#Rules for creating a test program
test.x: test.o $(OBJS_TEST) NtupleTools2Dict.o
	@echo "Creating test..."
	$(LD) $(LDFLAGS) $^ $(ROOTLIBS) $(MORELIBS)  -o $@
	@echo "$@ done"

#Rules for creating the linked library
%_sl.o : %.cpp
	$(CXX) -fPIC $(CXXFLAGS) -o $@ -c $<
NtupleTools2Dict_sl.o: NtupleTools2Dict.cxx
	 $(CXX) -fPIC $(CXXFLAGS) -D __NTHEADER___ -o $@ -c $<	
libRA4bDict.cpp : $(HEADERS_LIB) libRA4bDict_Linkdef.h 
	rootcint -f $@ -c -p $^
libRA4b.so : MTTOOLS $(OBJS_LIB) libRA4bDict_sl.o NtupleTools2Dict_sl.o 
	$(CXX) -shared $(LDFLAGS) $(ROOTLIBS) -o $@ $(OBJS_LIB) libRA4bDict_sl.o NtupleTools2Dict_sl.o $(OBJS_MTTOOLS_LIB)
	@echo "Made library!"


