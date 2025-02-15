SRC_NAME=nfs-utils-2.6.4
SRC_URL = https://master.dl.sourceforge.net/project/nfs/nfs-utils/2.6.4/nfs-utils-2.6.4.tar.xz?viasf=1
THISDIR = $(shell pwd)

CFLAGS += -ffunction-sections -fdata-sections -fvisibility=hidden
LDFLAGS += -Wl,--gc-sections

all: download_test extract_test config_test
#all: config_test
	$(MAKE) -j$(HOST_NCPU) -C $(SRC_NAME)

download_test:
	( if [ ! -f $(SRC_NAME).tar.xz ]; then \
		wget -t5 --timeout=20 --no-check-certificate -O $(SRC_NAME).tar.xz $(SRC_URL); \
	fi )

extract_test:
	( if [ ! -d $(SRC_NAME) ]; then \
		tar -xf $(SRC_NAME).tar.xz; \
	fi )

config_test:
	( if [ -f ./config_done ]; then \
		echo "the same configuration"; \
	else \
		make configure && touch config_done; \
	fi )

configure:
	( cd $(SRC_NAME) && \
	./autogen.sh ; \
	knfsd_cv_bsd_signals=no \
	PKG_CONFIG_PATH=$(STAGEDIR)/lib/pkgconfig \
	./configure \
		--prefix=/ \
		--disable-ipv6 \
		--disable-mount \
		--disable-libmount \
		--disable-mountconfig \
		--disable-nfsdcltrack \
		--disable-nfsv4 \
		--disable-nfsv41 \
		--disable-uuid \
		--disable-gss \
		--enable-tirpc \
		--without-gssglue \
		--without-krb5 \
		--without-tcp-wrappers \
		LIBS="-ltirpc" \
		--host=$(HOST_TARGET) \
		--build=$(HOST_BUILD) ; \
	)

clean:
	if [ -f $(SRC_NAME1)/Makefile ] ; then \
		$(MAKE) -C $(SRC_NAME) distclean ; \
	fi; \
	rm -f config_done

romfs:
	$(ROMFSINST) -p +x /usr/bin/nfsd.sh
	$(ROMFSINST) $(SRC_NAME)/utils/exportfs/exportfs /sbin/exportfs
	$(ROMFSINST) $(SRC_NAME)/utils/nfsd/nfsd /sbin/rpc.nfsd
	$(ROMFSINST) $(SRC_NAME)/utils/mountd/mountd /sbin/rpc.mountd
