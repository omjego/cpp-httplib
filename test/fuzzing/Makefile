
#CXX = clang++
# Do not add default sanitizer flags here as OSS-fuzz adds its own sanitizer flags.
CXXFLAGS += -ggdb -O0 -std=c++11 -DGTEST_USE_OWN_TR1_TUPLE -I../.. -I. -Wall -Wextra -Wtype-limits -Wconversion

OPENSSL_DIR = /usr/local/opt/openssl@1.1
OPENSSL_SUPPORT = -DCPPHTTPLIB_OPENSSL_SUPPORT -I$(OPENSSL_DIR)/include -L$(OPENSSL_DIR)/lib -lssl -lcrypto

ZLIB_SUPPORT = -DCPPHTTPLIB_ZLIB_SUPPORT -lz

BROTLI_DIR = /usr/local/opt/brotli
# BROTLI_SUPPORT = -DCPPHTTPLIB_BROTLI_SUPPORT -I$(BROTLI_DIR)/include -L$(BROTLI_DIR)/lib -lbrotlicommon -lbrotlienc -lbrotlidec
# BROTLI_STATIC_SUPPORT = -DCPPHTTPLIB_BROTLI_SUPPORT -I$(BROTLI_DIR)/include -L$(BROTLI_DIR)/lib -l:libbrotlicommon.a -l:libbrotlienc.a -l:libbrotlidec.a

# By default, use standalone_fuzz_target_runner.
# This runner does no fuzzing, but simply executes the inputs
# provided via parameters.
# Run e.g. "make all LIB_FUZZING_ENGINE=/path/to/libFuzzer.a"
# to link the fuzzer(s) against a real fuzzing engine.
# OSS-Fuzz will define its own value for LIB_FUZZING_ENGINE.
LIB_FUZZING_ENGINE ?= standalone_fuzz_target_runner.o

# Runs all the tests and also fuzz tests against seed corpus.
all : server_fuzzer
	./server_fuzzer corpus/*

# Fuzz target, so that you can choose which $(LIB_FUZZING_ENGINE) to use.
# -Wl,-Bstatic $(OPENSSL_SUPPORT)   -Wl,-Bdynamic -ldl  $(ZLIB_SUPPORT)  $(BROTLI_SUPPORT)
# $(BROTLI_STATIC_SUPPORT)
server_fuzzer : server_fuzzer.cc ../../httplib.h standalone_fuzz_target_runner.o
	$(CXX) $(CXXFLAGS) -o $@  $<  -Wl,-Bstatic $(OPENSSL_SUPPORT)  -Wl,-Bdynamic -ldl  $(ZLIB_SUPPORT)  $(LIB_FUZZING_ENGINE) -pthread
	zip -q -r server_fuzzer_seed_corpus.zip corpus

# Standalone fuzz runner, which just reads inputs from fuzzing/corpus/ dir and
# feeds it to server_fuzzer.
standalone_fuzz_target_runner.o : standalone_fuzz_target_runner.cpp
	$(CXX) $(CXXFLAGS) -c -o $@ $<

clean:
	rm -f server_fuzzer pem *.0 *.o *.1 *.srl *.zip
