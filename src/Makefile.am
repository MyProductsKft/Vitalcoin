# Copyright (c) 2013-2016 The Bitcoin Core developers
# Distributed under the MIT software license, see the accompanying
# file COPYING or http://www.opensource.org/licenses/mit-license.php.

DIST_SUBDIRS = secp256k1 univalue

AM_LDFLAGS = $(PTHREAD_CFLAGS) $(LIBTOOL_LDFLAGS) $(HARDENED_LDFLAGS)
AM_CXXFLAGS = $(HARDENED_CXXFLAGS) $(ERROR_CXXFLAGS)
AM_CPPFLAGS = $(HARDENED_CPPFLAGS)
EXTRA_LIBRARIES =

if EMBEDDED_UNIVALUE
LIBUNIVALUE = univalue/libunivalue.la

$(LIBUNIVALUE): $(wildcard univalue/lib/*) $(wildcard univalue/include/*)
	$(AM_V_at)$(MAKE) $(AM_MAKEFLAGS) -C $(@D) $(@F)
else
LIBUNIVALUE = $(UNIVALUE_LIBS)
endif

VITALCOIN_CONFIG_INCLUDES=-I$(builddir)/config
VITALCOIN_INCLUDES=-I$(builddir) -I$(builddir)/obj $(BDB_CPPFLAGS) $(BOOST_CPPFLAGS) $(LEVELDB_CPPFLAGS) $(CRYPTO_CFLAGS) $(SSL_CFLAGS)

VITALCOIN_INCLUDES += -I$(srcdir)/secp256k1/include
VITALCOIN_INCLUDES += $(UNIVALUE_CFLAGS)

LIBVITALCOIN_SERVER=libvitalcoin_server.a
LIBVITALCOIN_COMMON=libvitalcoin_common.a
LIBVITALCOIN_CONSENSUS=libvitalcoin_consensus.a
LIBVITALCOIN_CLI=libvitalcoin_cli.a
LIBVITALCOIN_UTIL=libvitalcoin_util.a
LIBVITALCOIN_CRYPTO=crypto/libvitalcoin_crypto.a
LIBVITALCOINQT=qt/libvitalcoinqt.a
LIBSECP256K1=secp256k1/libsecp256k1.la

if ENABLE_ZMQ
LIBVITALCOIN_ZMQ=libvitalcoin_zmq.a
endif
if BUILD_VITALCOIN_LIBS
LIBVITALCOINCONSENSUS=libvitalcoinconsensus.la
endif
if ENABLE_WALLET
LIBVITALCOIN_WALLET=libvitalcoin_wallet.a
endif

$(LIBSECP256K1): $(wildcard secp256k1/src/*) $(wildcard secp256k1/include/*)
	$(AM_V_at)$(MAKE) $(AM_MAKEFLAGS) -C $(@D) $(@F)

# Make is not made aware of per-object dependencies to avoid limiting building parallelization
# But to build the less dependent modules first, we manually select their order here:
EXTRA_LIBRARIES += \
  $(LIBVITALCOIN_CRYPTO) \
  $(LIBVITALCOIN_UTIL) \
  $(LIBVITALCOIN_COMMON) \
  $(LIBVITALCOIN_CONSENSUS) \
  $(LIBVITALCOIN_SERVER) \
  $(LIBVITALCOIN_CLI) \
  $(LIBVITALCOIN_WALLET) \
  $(LIBVITALCOIN_ZMQ)

lib_LTLIBRARIES = $(LIBVITALCOINCONSENSUS)

bin_PROGRAMS =
noinst_PROGRAMS =
TESTS =
BENCHMARKS =

if BUILD_VITALCOIND
  bin_PROGRAMS += vitalcoind
endif

if BUILD_VITALCOIN_UTILS
  bin_PROGRAMS += vitalcoin-cli vitalcoin-tx
endif

.PHONY: FORCE check-symbols check-security
# vitalcoin core #
VITALCOIN_CORE_H = \
  addrdb.h \
  addrman.h \
  base58.h \
  bloom.h \
  blockencodings.h \
  chain.h \
  chainparams.h \
  chainparamsbase.h \
  chainparamsseeds.h \
  checkpoints.h \
  checkqueue.h \
  clientversion.h \
  coins.h \
  compat.h \
  compat/byteswap.h \
  compat/endian.h \
  compat/sanity.h \
  compressor.h \
  consensus/consensus.h \
  consensus/tx_verify.h \
  core_io.h \
  core_memusage.h \
  cuckoocache.h \
  fs.h \
  httprpc.h \
  httpserver.h \
  indirectmap.h \
  init.h \
  key.h \
  keystore.h \
  dbwrapper.h \
  limitedmap.h \
  memusage.h \
  merkleblock.h \
  miner.h \
  net.h \
  net_processing.h \
  netaddress.h \
  netbase.h \
  netmessagemaker.h \
  noui.h \
  policy/feerate.h \
  policy/fees.h \
  policy/policy.h \
  policy/rbf.h \
  pow.h \
  protocol.h \
  random.h \
  reverse_iterator.h \
  reverselock.h \
  rpc/blockchain.h \
  rpc/client.h \
  rpc/mining.h \
  rpc/protocol.h \
  rpc/server.h \
  rpc/register.h \
  scheduler.h \
  script/sigcache.h \
  script/sign.h \
  script/standard.h \
  script/ismine.h \
  streams.h \
  support/allocators/secure.h \
  support/allocators/zeroafterfree.h \
  support/cleanse.h \
  support/events.h \
  support/lockedpool.h \
  sync.h \
  threadsafety.h \
  threadinterrupt.h \
  timedata.h \
  torcontrol.h \
  txdb.h \
  txmempool.h \
  ui_interface.h \
  undo.h \
  util.h \
  utilmoneystr.h \
  utiltime.h \
  validation.h \
  validationinterface.h \
  versionbits.h \
  wallet/coincontrol.h \
  wallet/crypter.h \
  wallet/db.h \
  wallet/feebumper.h \
  wallet/rpcwallet.h \
  wallet/wallet.h \
  wallet/walletdb.h \
  warnings.h \
  zmq/zmqabstractnotifier.h \
  zmq/zmqconfig.h\
  zmq/zmqnotificationinterface.h \
  zmq/zmqpublishnotifier.h


obj/build.h: FORCE
	@$(MKDIR_P) $(builddir)/obj
	@$(top_srcdir)/share/genbuild.sh "$(abs_top_builddir)/src/obj/build.h" \
	  "$(abs_top_srcdir)"
libvitalcoin_util_a-clientversion.$(OBJEXT): obj/build.h

# server: shared between vitalcoind and vitalcoin-qt
libvitalcoin_server_a_CPPFLAGS = $(AM_CPPFLAGS) $(VITALCOIN_INCLUDES) $(MINIUPNPC_CPPFLAGS) $(EVENT_CFLAGS) $(EVENT_PTHREADS_CFLAGS)
libvitalcoin_server_a_CXXFLAGS = $(AM_CXXFLAGS) $(PIE_FLAGS)
libvitalcoin_server_a_SOURCES = \
  addrdb.cpp \
  addrman.cpp \
  bloom.cpp \
  blockencodings.cpp \
  chain.cpp \
  checkpoints.cpp \
  consensus/tx_verify.cpp \
  httprpc.cpp \
  httpserver.cpp \
  init.cpp \
  dbwrapper.cpp \
  merkleblock.cpp \
  miner.cpp \
  net.cpp \
  net_processing.cpp \
  noui.cpp \
  policy/fees.cpp \
  policy/policy.cpp \
  policy/rbf.cpp \
  pow.cpp \
  rest.cpp \
  rpc/blockchain.cpp \
  rpc/mining.cpp \
  rpc/misc.cpp \
  rpc/net.cpp \
  rpc/rawtransaction.cpp \
  rpc/server.cpp \
  script/sigcache.cpp \
  script/ismine.cpp \
  timedata.cpp \
  torcontrol.cpp \
  txdb.cpp \
  txmempool.cpp \
  ui_interface.cpp \
  validation.cpp \
  validationinterface.cpp \
  versionbits.cpp \
  $(VITALCOIN_CORE_H)

if ENABLE_ZMQ
libvitalcoin_zmq_a_CPPFLAGS = $(VITALCOIN_INCLUDES) $(ZMQ_CFLAGS)
libvitalcoin_zmq_a_CXXFLAGS = $(AM_CXXFLAGS) $(PIE_FLAGS)
libvitalcoin_zmq_a_SOURCES = \
  zmq/zmqabstractnotifier.cpp \
  zmq/zmqnotificationinterface.cpp \
  zmq/zmqpublishnotifier.cpp
endif


# wallet: shared between vitalcoind and vitalcoin-qt, but only linked
# when wallet enabled
libvitalcoin_wallet_a_CPPFLAGS = $(AM_CPPFLAGS) $(VITALCOIN_INCLUDES)
libvitalcoin_wallet_a_CXXFLAGS = $(AM_CXXFLAGS) $(PIE_FLAGS)
libvitalcoin_wallet_a_SOURCES = \
  wallet/crypter.cpp \
  wallet/db.cpp \
  wallet/feebumper.cpp \
  wallet/rpcdump.cpp \
  wallet/rpcwallet.cpp \
  wallet/wallet.cpp \
  wallet/walletdb.cpp \
  $(VITALCOIN_CORE_H)

# crypto primitives library
crypto_libvitalcoin_crypto_a_CPPFLAGS = $(AM_CPPFLAGS) $(VITALCOIN_CONFIG_INCLUDES)
crypto_libvitalcoin_crypto_a_CXXFLAGS = $(AM_CXXFLAGS) $(PIE_FLAGS)
crypto_libvitalcoin_crypto_a_SOURCES = \
  crypto/aes.cpp \
  crypto/aes.h \
  crypto/chacha20.h \
  crypto/chacha20.cpp \
  crypto/common.h \
  crypto/hmac_sha256.cpp \
  crypto/hmac_sha256.h \
  crypto/hmac_sha512.cpp \
  crypto/hmac_sha512.h \
  crypto/ripemd160.cpp \
  crypto/ripemd160.h \
  crypto/sha1.cpp \
  crypto/sha1.h \
  crypto/sha256.cpp \
  crypto/sha256.h \
  crypto/sha512.cpp \
  crypto/sha512.h

if EXPERIMENTAL_ASM
crypto_libvitalcoin_crypto_a_SOURCES += crypto/sha256_sse4.cpp
endif

# consensus: shared between all executables that validate any consensus rules.
libvitalcoin_consensus_a_CPPFLAGS = $(AM_CPPFLAGS) $(VITALCOIN_INCLUDES)
libvitalcoin_consensus_a_CXXFLAGS = $(AM_CXXFLAGS) $(PIE_FLAGS)
libvitalcoin_consensus_a_SOURCES = \
  amount.h \
  arith_uint256.cpp \
  arith_uint256.h \
  consensus/consensus.cpp \
  consensus/merkle.cpp \
  consensus/merkle.h \
  consensus/params.h \
  consensus/validation.h \
  hash.cpp \
  hash.h \
  prevector.h \
  primitives/block.cpp \
  primitives/block.h \
  primitives/transaction.cpp \
  primitives/transaction.h \
  pubkey.cpp \
  pubkey.h \
  script/vitalcoinconsensus.cpp \
  script/interpreter.cpp \
  script/interpreter.h \
  script/script.cpp \
  script/script.h \
  script/script_error.cpp \
  script/script_error.h \
  serialize.h \
  tinyformat.h \
  uint256.cpp \
  uint256.h \
  utilstrencodings.cpp \
  utilstrencodings.h \
  version.h

# common: shared between vitalcoind, and vitalcoin-qt and non-server tools
libvitalcoin_common_a_CPPFLAGS = $(AM_CPPFLAGS) $(VITALCOIN_INCLUDES)
libvitalcoin_common_a_CXXFLAGS = $(AM_CXXFLAGS) $(PIE_FLAGS)
libvitalcoin_common_a_SOURCES = \
  base58.cpp \
  chainparams.cpp \
  coins.cpp \
  compressor.cpp \
  core_read.cpp \
  core_write.cpp \
  key.cpp \
  keystore.cpp \
  netaddress.cpp \
  netbase.cpp \
  policy/feerate.cpp \
  protocol.cpp \
  scheduler.cpp \
  script/sign.cpp \
  script/standard.cpp \
  warnings.cpp \
  $(VITALCOIN_CORE_H)

# util: shared between all executables.
# This library *must* be included to make sure that the glibc
# backward-compatibility objects and their sanity checks are linked.
libvitalcoin_util_a_CPPFLAGS = $(AM_CPPFLAGS) $(VITALCOIN_INCLUDES)
libvitalcoin_util_a_CXXFLAGS = $(AM_CXXFLAGS) $(PIE_FLAGS)
libvitalcoin_util_a_SOURCES = \
  support/lockedpool.cpp \
  chainparamsbase.cpp \
  clientversion.cpp \
  compat/glibc_sanity.cpp \
  compat/glibcxx_sanity.cpp \
  compat/strnlen.cpp \
  fs.cpp \
  random.cpp \
  rpc/protocol.cpp \
  support/cleanse.cpp \
  sync.cpp \
  threadinterrupt.cpp \
  util.cpp \
  utilmoneystr.cpp \
  utilstrencodings.cpp \
  utiltime.cpp \
  $(VITALCOIN_CORE_H)

if GLIBC_BACK_COMPAT
libvitalcoin_util_a_SOURCES += compat/glibc_compat.cpp
endif

# cli: shared between vitalcoin-cli and vitalcoin-qt
libvitalcoin_cli_a_CPPFLAGS = $(AM_CPPFLAGS) $(VITALCOIN_INCLUDES)
libvitalcoin_cli_a_CXXFLAGS = $(AM_CXXFLAGS) $(PIE_FLAGS)
libvitalcoin_cli_a_SOURCES = \
  rpc/client.cpp \
  $(VITALCOIN_CORE_H)

nodist_libvitalcoin_util_a_SOURCES = $(srcdir)/obj/build.h
#

# vitalcoind binary #
vitalcoind_SOURCES = vitalcoind.cpp
vitalcoind_CPPFLAGS = $(AM_CPPFLAGS) $(VITALCOIN_INCLUDES)
vitalcoind_CXXFLAGS = $(AM_CXXFLAGS) $(PIE_FLAGS)
vitalcoind_LDFLAGS = $(RELDFLAGS) $(AM_LDFLAGS) $(LIBTOOL_APP_LDFLAGS)

if TARGET_WINDOWS
vitalcoind_SOURCES += vitalcoind-res.rc
endif

vitalcoind_LDADD = \
  $(LIBVITALCOIN_SERVER) \
  $(LIBVITALCOIN_COMMON) \
  $(LIBUNIVALUE) \
  $(LIBVITALCOIN_UTIL) \
  $(LIBVITALCOIN_WALLET) \
  $(LIBVITALCOIN_ZMQ) \
  $(LIBVITALCOIN_CONSENSUS) \
  $(LIBVITALCOIN_CRYPTO) \
  $(LIBLEVELDB) \
  $(LIBLEVELDB_SSE42) \
  $(LIBMEMENV) \
  $(LIBSECP256K1)

vitalcoind_LDADD += $(BOOST_LIBS) $(BDB_LIBS) $(SSL_LIBS) $(CRYPTO_LIBS) $(MINIUPNPC_LIBS) $(EVENT_PTHREADS_LIBS) $(EVENT_LIBS) $(ZMQ_LIBS)

# vitalcoin-cli binary #
vitalcoin_cli_SOURCES = vitalcoin-cli.cpp
vitalcoin_cli_CPPFLAGS = $(AM_CPPFLAGS) $(VITALCOIN_INCLUDES) $(EVENT_CFLAGS)
vitalcoin_cli_CXXFLAGS = $(AM_CXXFLAGS) $(PIE_FLAGS)
vitalcoin_cli_LDFLAGS = $(RELDFLAGS) $(AM_LDFLAGS) $(LIBTOOL_APP_LDFLAGS)

if TARGET_WINDOWS
vitalcoin_cli_SOURCES += vitalcoin-cli-res.rc
endif

vitalcoin_cli_LDADD = \
  $(LIBVITALCOIN_CLI) \
  $(LIBUNIVALUE) \
  $(LIBVITALCOIN_UTIL) \
  $(LIBVITALCOIN_CRYPTO)

vitalcoin_cli_LDADD += $(BOOST_LIBS) $(SSL_LIBS) $(CRYPTO_LIBS) $(EVENT_LIBS)
#

# vitalcoin-tx binary #
vitalcoin_tx_SOURCES = vitalcoin-tx.cpp
vitalcoin_tx_CPPFLAGS = $(AM_CPPFLAGS) $(VITALCOIN_INCLUDES)
vitalcoin_tx_CXXFLAGS = $(AM_CXXFLAGS) $(PIE_FLAGS)
vitalcoin_tx_LDFLAGS = $(RELDFLAGS) $(AM_LDFLAGS) $(LIBTOOL_APP_LDFLAGS)

if TARGET_WINDOWS
vitalcoin_tx_SOURCES += vitalcoin-tx-res.rc
endif

vitalcoin_tx_LDADD = \
  $(LIBUNIVALUE) \
  $(LIBVITALCOIN_COMMON) \
  $(LIBVITALCOIN_UTIL) \
  $(LIBVITALCOIN_CONSENSUS) \
  $(LIBVITALCOIN_CRYPTO) \
  $(LIBSECP256K1)

vitalcoin_tx_LDADD += $(BOOST_LIBS) $(CRYPTO_LIBS)
#

# vitalcoinconsensus library #
if BUILD_VITALCOIN_LIBS
include_HEADERS = script/vitalcoinconsensus.h
libvitalcoinconsensus_la_SOURCES = $(crypto_libvitalcoin_crypto_a_SOURCES) $(libvitalcoin_consensus_a_SOURCES)

if GLIBC_BACK_COMPAT
  libvitalcoinconsensus_la_SOURCES += compat/glibc_compat.cpp
endif

libvitalcoinconsensus_la_LDFLAGS = $(AM_LDFLAGS) -no-undefined $(RELDFLAGS)
libvitalcoinconsensus_la_LIBADD = $(LIBSECP256K1)
libvitalcoinconsensus_la_CPPFLAGS = $(AM_CPPFLAGS) -I$(builddir)/obj -I$(srcdir)/secp256k1/include -DBUILD_VITALCOIN_INTERNAL
libvitalcoinconsensus_la_CXXFLAGS = $(AM_CXXFLAGS) $(PIE_FLAGS)

endif
#

CTAES_DIST =  crypto/ctaes/bench.c
CTAES_DIST += crypto/ctaes/ctaes.c
CTAES_DIST += crypto/ctaes/ctaes.h
CTAES_DIST += crypto/ctaes/README.md
CTAES_DIST += crypto/ctaes/test.c

CLEANFILES = $(EXTRA_LIBRARIES)

CLEANFILES += *.gcda *.gcno
CLEANFILES += compat/*.gcda compat/*.gcno
CLEANFILES += consensus/*.gcda consensus/*.gcno
CLEANFILES += crypto/*.gcda crypto/*.gcno
CLEANFILES += policy/*.gcda policy/*.gcno
CLEANFILES += primitives/*.gcda primitives/*.gcno
CLEANFILES += script/*.gcda script/*.gcno
CLEANFILES += support/*.gcda support/*.gcno
CLEANFILES += univalue/*.gcda univalue/*.gcno
CLEANFILES += wallet/*.gcda wallet/*.gcno
CLEANFILES += wallet/test/*.gcda wallet/test/*.gcno
CLEANFILES += zmq/*.gcda zmq/*.gcno

DISTCLEANFILES = obj/build.h

EXTRA_DIST = $(CTAES_DIST)


config/vitalcoin-config.h: config/stamp-h1
	@$(MAKE) -C $(top_builddir) $(subdir)/$(@)
config/stamp-h1: $(top_srcdir)/$(subdir)/config/vitalcoin-config.h.in $(top_builddir)/config.status
	$(AM_V_at)$(MAKE) -C $(top_builddir) $(subdir)/$(@)
$(top_srcdir)/$(subdir)/config/vitalcoin-config.h.in:  $(am__configure_deps)
	$(AM_V_at)$(MAKE) -C $(top_srcdir) $(subdir)/config/vitalcoin-config.h.in

clean-local:
	-$(MAKE) -C secp256k1 clean
	-$(MAKE) -C univalue clean
	-rm -f leveldb/*/*.gcda leveldb/*/*.gcno leveldb/helpers/memenv/*.gcda leveldb/helpers/memenv/*.gcno
	-rm -f config.h
	-rm -rf test/__pycache__

.rc.o:
	@test -f $(WINDRES)
	## FIXME: How to get the appropriate modulename_CPPFLAGS in here?
	$(AM_V_GEN) $(WINDRES) $(DEFS) $(DEFAULT_INCLUDES) $(INCLUDES) $(CPPFLAGS) -DWINDRES_PREPROC -i $< -o $@

.mm.o:
	$(AM_V_CXX) $(OBJCXX) $(DEFS) $(DEFAULT_INCLUDES) $(INCLUDES) $(AM_CPPFLAGS) \
	  $(CPPFLAGS) $(AM_CXXFLAGS) $(QT_INCLUDES) $(AM_CXXFLAGS) $(PIE_FLAGS) $(CXXFLAGS) -c -o $@ $<

check-symbols: $(bin_PROGRAMS)
if GLIBC_BACK_COMPAT
	@echo "Checking glibc back compat..."
	$(AM_V_at) READELF=$(READELF) CPPFILT=$(CPPFILT) $(top_srcdir)/contrib/devtools/symbol-check.py < $(bin_PROGRAMS)
endif

check-security: $(bin_PROGRAMS)
if HARDEN
	@echo "Checking binary security..."
	$(AM_V_at) READELF=$(READELF) OBJDUMP=$(OBJDUMP) $(top_srcdir)/contrib/devtools/security-check.py < $(bin_PROGRAMS)
endif

%.pb.cc %.pb.h: %.proto
	@test -f $(PROTOC)
	$(AM_V_GEN) $(PROTOC) --cpp_out=$(@D) --proto_path=$(<D) $<

if EMBEDDED_LEVELDB
include Makefile.leveldb.include
endif

if ENABLE_TESTS
include Makefile.test.include
endif

if ENABLE_BENCH
include Makefile.bench.include
endif

if ENABLE_QT
include Makefile.qt.include
endif

if ENABLE_QT_TESTS
include Makefile.qttest.include
endif
