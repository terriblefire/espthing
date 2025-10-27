# Makefile for Eagle schematic netlist generation and CAM output
# Uses lxml library in a virtual environment for netlist generation
# Uses terriblefire78/eagle:v1 Docker container for CAM output

# Variables for netlist/PCF generation
VENV_DIR = .venv
VENV_MARKER = $(VENV_DIR)/.installed
PYTHON = $(VENV_DIR)/bin/python
PIP = $(VENV_DIR)/bin/pip
NETLIST_SCRIPT = generate_netlist.py
PCF_SCRIPT = generate_pcf.py

# Find the .sch file
SCH_FILE = $(wildcard *.sch)
NET_FILE = $(SCH_FILE:.sch=.net)

# Variables for CAM output (Gerber generation)
BOARDPREFIX := espthing
BOARD := $(BOARDPREFIX).brd
CAMFILE := jlcpcb_4_layer_v9.cam
GITVERSION := $(shell git rev-parse --short HEAD)
DATESTRING := $(shell date +"%Y_%m_%d")
GERBERS := $(BOARDPREFIX)_$(GITVERSION)_$(DATESTRING)
EAGLE_DOCKER := docker run --rm -v $(CURDIR):/work -w /work terriblefire78/eagle:v1 eagle

# Default target
.PHONY: all
all: $(PCF_FILE) $(GERBERS).zip 


.PHONY: pcf
pcf: $(PCF_FILE)

# CAM output (Gerber generation) targets
.PHONY: gerbers camfiles
gerbers: $(GERBERS).zip

camfiles: $(GERBERS)

$(GERBERS).zip: $(GERBERS)
	@echo "Creating Gerber archive..."
	zip -r $@ $(GERBERS)/* $(GERBERS)/*/*
	@echo "Gerber archive created: $@"

$(GERBERS): $(BOARD) $(CAMFILE)
	@echo "Generating Gerbers for $(BOARD) using $(CAMFILE)..."
	mkdir -p $(GERBERS)
	$(EAGLE_DOCKER) -X -dCAMJOB -j$(CAMFILE) -o$(GERBERS) $(BOARD)
	@echo "Gerbers generated in $(GERBERS)/"

# Clean generated files
.PHONY: clean
clean: clean-gerbers
	rm -f $(NET_FILE) $(PCF_FILE)
	@echo "Cleaned generated netlist and PCF files"

.PHONY: clean-gerbers
clean-gerbers:
	rm -rf $(BOARDPREFIX)_*_*/ 2>/dev/null || rm -rf $(BOARDPREFIX)_*_*/ 2>/dev/null || true
	rm -f $(BOARDPREFIX)_*_*.zip $(DRILL_CAMFILE)
	@echo "Cleaned Gerber files and directories"

# Clean everything including venv
.PHONY: distclean
distclean: clean clean-gerbers
	rm -rf $(VENV_DIR)
	@echo "Cleaned virtual environment"

# Show help
.PHONY: help
help:
	@echo "Eagle Netlist, PCF Generator, and CAM Output Makefile"
	@echo ""
	@echo "Targets:"
	@echo "  all           - Generate netlist and PCF (default)"
	@echo "  pcf           - Generate PCF file from netlist"
	@echo "  gerbers       - Generate Gerber files and create ZIP archive"
	@echo "  camfiles      - Generate Gerber files only (no ZIP)"
	@echo "  clean         - Remove generated netlist and PCF files"
	@echo "  clean-gerbers - Remove Gerber files and directories"
	@echo "  distclean     - Remove all generated files and virtual environment"
	@echo "  help          - Show this help message"
	@echo ""
	@echo "Input:  $(SCH_FILE), $(BOARD)"
	@echo "Output: $(NET_FILE), $(PCF_FILE), $(GERBERS).zip"
