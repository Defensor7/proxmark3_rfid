
# This code is licensed to you under the terms of the GNU GPL, version 2 or,
# at your option, any later version. See the LICENSE.txt file for the text of
# the license.
#-----------------------------------------------------------------------------
# Makefile for armsrc, see ../common/Makefile.common for common settings
#-----------------------------------------------------------------------------

APP_INCLUDES = apps.h

#remove one of the following defines and comment out the relevant line
#in the next section to remove that particular feature from compilation.
# NO space,TABs after the "\" sign.  
APP_CFLAGS = -DWITH_CRC \
			 -DON_DEVICE \
			 -DWITH_LF \
			 -DWITH_HITAG \
 			 -DWITH_ISO15693 \
			 -DWITH_LEGICRF \
			 -DWITH_ISO14443b \
			 -DWITH_ISO14443a \
			 -DWITH_ICLASS \
			 -DWITH_FELICA \
			 -DWITH_FLASH \
			 -DWITH_SMARTCARD \
			 -DWITH_HFSNOOP \
			 -DWITH_LF_SAMYRUN \
			 -DWITH_FPC \
			 -fno-strict-aliasing -ffunction-sections -fdata-sections

### IMPORTANT -  move the commented variable below this line
#			 -DWITH_LCD \
#			 -DWITH_EMV \
#			 -DWITH_FPC \
#
# Standalone Mods
#-------------------------------------------------------
#			 -DWITH_LF_ICERUN
#			 -DWITH_LF_SAMYRUN
#			 -DWITH_LF_PROXBRUTE
#			 -DWITH_LF_HIDBRUTE
#			 -DWITH_HF_YOUNG
#			 -DWITH_HF_MATTYRUN
#			 -DWITH_HF_COLIN


SRC_LCD = fonts.c LCD.c
SRC_LF = lfops.c hitag2.c hitagS.c lfsampling.c pcf7931.c lfdemod.c
SRC_ISO15693 = iso15693.c iso15693tools.c
#SRC_ISO14443a = iso14443a.c mifareutil.c mifarecmd.c epa.c mifaresim.c
SRC_ISO14443a = iso14443a.c mifareutil.c mifarecmd.c epa.c 
SRC_ISO14443b = iso14443b.c
SRC_FELICA = felica.c
SRC_CRAPTO1 = crypto1.c des.c aes.c desfire_key.c desfire_crypto.c mifaredesfire.c
SRC_CRC = crc.c crc16.c crc32.c 
SRC_ICLASS = iclass.c optimized_cipher.c
SRC_LEGIC = legicrf.c legicrfsim.c legic_prng.c
SRC_BEE = bee.c

# RDV40 related hardware support
ifneq (,$(findstring WITH_FLASH,$(APP_CFLAGS)))
	SRC_FLASH = flashmem.c
else
	SRC_FLASH = 
endif

ifneq (,$(findstring WITH_SMARTCARD,$(APP_CFLAGS)))
	SRC_SMARTCARD = i2c.c
else
	SRC_SMARTCARD = 
endif

ifneq (,$(findstring WITH_FPC,$(APP_CFLAGS)))
	SRC_FPC = usart.c
else
	SRC_FPC = 
endif

# Generic standalone Mode injection of source code
SRC_STANDALONE =
# WITH_LF_ICERUN
ifneq (,$(findstring WITH_LF_ICERUN,$(APP_CFLAGS)))
	SRC_STANDALONE =
endif
# WITH_LF_SAMYRUN
ifneq (,$(findstring WITH_LF_SAMYRUN,$(APP_CFLAGS)))
	SRC_STANDALONE = lf_samyrun.c
endif
# WITH_LF_PROXBRUTE
ifneq (,$(findstring WITH_LF_PROXBRUTE,$(APP_CFLAGS)))
	SRC_STANDALONE = lf_proxbrute.c
endif
# WITH_LF_HIDBRUTE
ifneq (,$(findstring WITH_LF_HIDBRUTE,$(APP_CFLAGS)))
	SRC_STANDALONE = lf_hidbrute.c 
endif
# WITH_HF_YOUNG
ifneq (,$(findstring WITH_HF_YOUNG,$(APP_CFLAGS)))
	SRC_STANDALONE = hf_young.c
endif
# WITH_HF_MATTYRUN
ifneq (,$(findstring WITH_HF_MATTYRUN,$(APP_CFLAGS)))
	SRC_STANDALONE = hf_mattyrun.c
endif
# WITH_HF_COLIN
ifneq (,$(findstring WITH_HF_COLIN,$(APP_CFLAGS)))
	SRC_STANDALONE = vtsend.c hf_colin.c
endif

#the FPGA bitstream files. Note: order matters!
FPGA_BITSTREAMS = fpga_lf.bit fpga_hf.bit

#the zlib source files required for decompressing the fpga config at run time
SRC_ZLIB = inflate.c inffast.c inftrees.c adler32.c zutil.c
#additional defines required to compile zlib
ZLIB_CFLAGS = -DZ_SOLO -DZ_PREFIX -DNO_GZIP -DZLIB_PM3_TUNED
APP_CFLAGS += $(ZLIB_CFLAGS)
# zlib includes:
APP_CFLAGS += -I../zlib

# stdint.h provided locally until GCC 4.5 becomes C99 compliant
APP_CFLAGS += -I.

# Compile these in thumb mode (small size)
THUMBSRC = start.c \
	protocols.c \
	$(SRC_LCD) \
	$(SRC_ISO15693) \
	$(SRC_LF) \
	$(SRC_ZLIB) \
	$(SRC_LEGIC) \
	$(SRC_FLASH) \
	$(SRC_SMARTCARD) \
	$(SRC_FPC) \
	appmain.c \
	printf.c \
	util.c \
	string.c \
	BigBuf.c \
	ticks.c \
	random.c \
	hfsnoop.c


# These are to be compiled in ARM mode
ARMSRC = fpgaloader.c \
	$(SRC_ISO14443a) \
	$(SRC_ISO14443b) \
	$(SRC_CRAPTO1) \
	$(SRC_ICLASS) \
	$(SRC_EMV) \
	$(SRC_CRC) \
	$(SRC_FELICA) \
	$(SRC_STANDALONE) \
	parity.c \
	usb_cdc.c \
	cmd.c
	
VERSIONSRC = version.c \
	fpga_version_info.c
	
# Do not move this inclusion before the definition of {THUMB,ASM,ARM}SRC
include ../common/Makefile.common

COMMON_FLAGS =  -Os 

OBJS = $(OBJDIR)/fullimage.s19 
FPGA_COMPRESSOR = ../client/fpga_compress

all: $(OBJS)

.DELETE_ON_ERROR:

# version.c should be remade on every compilation
.PHONY: version.c
version.c: default_version.c
	perl ../tools/mkversion.pl .. > $@ || $(COPY) $^ $@ 

fpga_version_info.c: $(FPGA_BITSTREAMS) $(FPGA_COMPRESSOR)
	$(FPGA_COMPRESSOR) -v $(filter %.bit,$^) $@

$(OBJDIR)/fpga_all.o: $(OBJDIR)/fpga_all.bit.z
	$(OBJCOPY) -O elf32-littlearm -I binary -B arm --prefix-sections=fpga_all_bit $^ $@

$(OBJDIR)/fpga_all.bit.z: $(FPGA_BITSTREAMS) $(FPGA_COMPRESSOR)
	$(FPGA_COMPRESSOR) $(filter %.bit,$^) $@  

$(FPGA_COMPRESSOR):
		make -C ../client $(notdir $(FPGA_COMPRESSOR))
		
$(OBJDIR)/fullimage.stage1.elf: $(VERSIONOBJ) $(OBJDIR)/fpga_all.o $(THUMBOBJ) $(ARMOBJ)
	$(CC) $(LDFLAGS) -Wl,-T,ldscript,-Map,$(patsubst %.elf,%.map,$@) -o $@ $^ $(LIBS)

$(OBJDIR)/fullimage.nodata.bin: $(OBJDIR)/fullimage.stage1.elf
	$(OBJCOPY) -O binary -I elf32-littlearm --remove-section .data $^ $@
	
$(OBJDIR)/fullimage.nodata.o: $(OBJDIR)/fullimage.nodata.bin
	$(OBJCOPY) -O elf32-littlearm -I binary -B arm --rename-section .data=stage1_image $^ $@

$(OBJDIR)/fullimage.data.bin: $(OBJDIR)/fullimage.stage1.elf
	$(OBJCOPY) -O binary -I elf32-littlearm --only-section .data $^ $@

$(OBJDIR)/fullimage.data.bin.z: $(OBJDIR)/fullimage.data.bin $(FPGA_COMPRESSOR)
	$(FPGA_COMPRESSOR) $(filter %.bin,$^) $@  
	
$(OBJDIR)/fullimage.data.o: $(OBJDIR)/fullimage.data.bin.z
	$(OBJCOPY) -O elf32-littlearm -I binary -B arm --rename-section .data=compressed_data $^ $@

$(OBJDIR)/fullimage.elf: $(OBJDIR)/fullimage.nodata.o $(OBJDIR)/fullimage.data.o
	$(CC) $(LDFLAGS) -Wl,-T,ldscript,-e,_osimage_entry,-Map,$(patsubst %.elf,%.map,$@) -o $@ $^

tarbin: $(OBJS)
	$(TAR) $(TARFLAGS) ../proxmark3-$(platform)-bin.tar $(OBJS:%=armsrc/%) $(OBJS:%.s19=armsrc/%.elf)

clean:
	$(DELETE) $(OBJDIR)$(PATHSEP)*.o
	$(DELETE) $(OBJDIR)$(PATHSEP)*.elf
	$(DELETE) $(OBJDIR)$(PATHSEP)*.s19
	$(DELETE) $(OBJDIR)$(PATHSEP)*.map
	$(DELETE) $(OBJDIR)$(PATHSEP)*.d
	$(DELETE) $(OBJDIR)$(PATHSEP)*.z
	$(DELETE) $(OBJDIR)$(PATHSEP)*.bin
	$(DELETE) version.c

.PHONY: all clean help
help:
	@echo Multi-OS Makefile, you are running on $(DETECTED_OS)
	@echo Possible targets:
	@echo +	all    - Build the full image $(OBJDIR)/fullimage.s19
	@echo +	clean             - Clean $(OBJDIR)
