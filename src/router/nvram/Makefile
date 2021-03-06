#
# Linux NVRAM Makefile
#
# Copyright (C) 2012, Broadcom Corporation. All Rights Reserved.
# 
# Permission to use, copy, modify, and/or distribute this software for any
# purpose with or without fee is hereby granted, provided that the above
# copyright notice and this permission notice appear in all copies.
# 
# THE SOFTWARE IS PROVIDED "AS IS" AND THE AUTHOR DISCLAIMS ALL WARRANTIES
# WITH REGARD TO THIS SOFTWARE INCLUDING ALL IMPLIED WARRANTIES OF
# MERCHANTABILITY AND FITNESS. IN NO EVENT SHALL THE AUTHOR BE LIABLE FOR ANY
# SPECIAL, DIRECT, INDIRECT, OR CONSEQUENTIAL DAMAGES OR ANY DAMAGES
# WHATSOEVER RESULTING FROM LOSS OF USE, DATA OR PROFITS, WHETHER IN AN ACTION
# OF CONTRACT, NEGLIGENCE OR OTHER TORTIOUS ACTION, ARISING OUT OF OR IN
# CONNECTION WITH THE USE OR PERFORMANCE OF THIS SOFTWARE.
#
# $Id: Makefile 271576 2011-07-12 20:31:22Z $
#

include $(TOP)/.config

ifeq ($(CONFIG_ACOS_MODULES),y)
ACOSTOPDIR= $(SRCBASE)/../ap/acos
ACOSNVRAM= $(ACOSTOPDIR)/nvram
endif

#foxconn wklin add start, 05/12/2009, for nvram_loaddefault MTD device
CFLAGS += -DLINUX26
#foxconn wklin add end, 05/12/2009

CFLAGS	+= -I. -I$(SRCBASE)/include -I$(SRCBASE)/../project/acos/include -Wall -DINCLUDE_WAN_LAN_SUBNET
CFLAGS	+= -DINCLUDE_WDS -DINCLUDE_QOS -DBANDWIDTH_DETECT_17


ifneq ($(PROFILE),) ## R6300v2 || R6250
CFLAGS  += -DX_ST_ML
endif


#CFLAGS	+= -g -DDEBUG
CFLAGS	+= -s -O2
LDFLAGS += -L.

ifeq ($(CONFIG_ACOS_MODULES),y)
CFLAGS	+= -DACOS_MODULES_ENABLE
CFLAGS	+= -I$(ACOSTOPDIR)/nvram -I$(ACOSTOPDIR)/include
LDFLAGS	+= -L$(ACOSTOPDIR)/include -L$(ACOSTOPDIR)/nvram
endif

OBJS := nvram_linux.o
ifeq ($(CONFIG_ACOS_MODULES),y)
OBJS += $(ACOSNVRAM)/acosNvramConfig.o
OBJS += $(ACOSNVRAM)/defaults.o
endif

CFLAGS += -fPIC

vpath %.c $(SRCBASE)/shared/nvram

all: libnvram.so $(if $(CONFIG_NVRAM_UTILITY),nvram)

install: all
	install -D libnvram.so $(INSTALLDIR)/usr/lib/libnvram.so
	$(STRIP) $(INSTALLDIR)/usr/lib/libnvram.so
ifneq ($(CONFIG_NVRAM_UTILITY),)
	install -D nvram $(INSTALLDIR)/usr/sbin/nvram
	$(STRIP) $(INSTALLDIR)/usr/sbin/nvram
else
	# Utility not wanted: get rid of any leftover one
	rm -f $(INSTALLDIR)/usr/sbin/nvram
endif


clean:
	rm -f *.o *.a *.so nvram
ifeq ($(CONFIG_ACOS_MODULES),y)
	rm -f $(ACOSNVRAM)/*.o
endif	

libnvram.so: $(OBJS)
	$(LD) -fPIC -shared -o $@ $^

nvram: main.o libnvram.so
	$(CC) $(CFLAGS) -o $@ $< $(LDFLAGS) -lnvram
