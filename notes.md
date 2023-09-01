Installing environment: Ubuntu LTS 18.04
## brotli
rm patched sf
make sf SF_COLLECT=1

make clean
make sf

cd /home/zhongfa/benchmarks/brotli
honggfuzz  --run_time 3600 --exit_upon_crash -Q --no_fb_timeout 1 --timeout 120 -n 1 -f ./ -l hongg.log -- ./sf ___FILE___ 2>&1 | analyzer collect -r hongg.log -o analyzer.json -b sf >errors.log 2>&1
analyzer minimize analyzer.json -o minimal.json
analyzer aggregate minimal.json -s $(llvm-7.0.1-config --bindir)/llvm-symbolizer -b ./sf -o aggregated.json
analyzer query aggregated.json -o whitelist.txt
make patched PERF=1

## http
rm patched sf
make sf SF_COLLECT=1

make clean
make sf

cd /home/zhongfa/benchmarks/http-parser
honggfuzz  --run_time 3600 --exit_upon_crash -Q --no_fb_timeout 1 --timeout 120 -n 1 -f ./ -l hongg.log -- ./sf ___FILE___ 2>&1 | analyzer collect -r hongg.log -o analyzer.json -b sf >errors.log 2>&1
analyzer minimize analyzer.json -o minimal.json
analyzer aggregate minimal.json -s $(llvm-7.0.1-config --bindir)/llvm-symbolizer -b ./sf -o aggregated.json
analyzer query aggregated.json -o whitelist.txt
make patched PERF=1
./patched large.txt

## jsmn
rm patched sf
make sf SF_COLLECT=1

make clean
make sf

cd /home/zhongfa/benchmarks/jsmn
honggfuzz  --run_time 3600 --exit_upon_crash -Q --no_fb_timeout 1 --timeout 120 -n 1 -f ./ -l hongg.log -- ./sf ___FILE___ 2>&1 | analyzer collect -r hongg.log -o analyzer.json -b sf >errors.log 2>&1
analyzer minimize analyzer.json -o minimal.json
analyzer aggregate minimal.json -s $(llvm-7.0.1-config --bindir)/llvm-symbolizer -b ./sf -o aggregated.json
analyzer query aggregated.json -o whitelist.txt
make patched PERF=1


## libhtp
cp -r /home/zhongfa/SpecFuzz/example/usesec20/libhtp/. /home/zhongfa/benchmarks/libhtp-benchmark/
cd /home/zhongfa/benchmarks/libhtp-benchmark/libhtp
sudo chmod u+x autogen.sh
./autogen.sh
cd /home/zhongfa/benchmarks/libhtp-benchmark
make sf SF_COLLECT=1
make clean

honggfuzz  --run_time 3600 --exit_upon_crash -Q --no_fb_timeout 1 --timeout 120 -n 1 -f ./ -l hongg.log -- ./sf ___FILE___ 2>&1 | analyzer collect -r hongg.log -o analyzer.json -b sf >errors.log 2>&1
analyzer minimize analyzer.json -o minimal.json
analyzer aggregate minimal.json -s $(llvm-7.0.1-config --bindir)/llvm-symbolizer -b ./sf -o aggregated.json
analyzer query aggregated.json -o whitelist.txt
make patched PERF=1
srcdir=test/files ./patched --gtest_filter=Benchmark.ConnectionWithManyTransactions

## libyaml
cp -r /home/zhongfa/SpecFuzz/example/usesec20/libyaml/. /home/zhongfa/benchmarks/libyaml-benchmark
cp /home/zhongfa/SpecFuzz/example/usesec20/libyaml/fuzzer.c /home/zhongfa/benchmarks/libyaml-benchmark/libyaml/tests
cd /home/zhongfa/benchmarks/libyaml-benchmark/libyaml/tests
sed -i 's/run-parser-test-suite run-emitter-test-suite/run-parser-test-suite run-emitter-test-suite fuzzer/' Makefile.am

cd /home/zhongfa/benchmarks/libyaml-benchmark/libyaml 
./bootstrap
cd /home/zhongfa/benchmarks/libyaml-benchmark/
make sf SF_COLLECT=1 #sudo make sf SF_COLLECT=1 HONGG_SRC=/usr/local/honggfuzz-589a9fb92/src
make clean
honggfuzz  --run_time 3600 --exit_upon_crash -Q --no_fb_timeout 1 --timeout 120 -n 1 -f ./ -l hongg.log -- ./sf ___FILE___ 2>&1 | analyzer collect -r hongg.log -o analyzer.json -b sf >errors.log 2>&1
analyzer minimize analyzer.json -o minimal.json
analyzer aggregate minimal.json -s $(llvm-7.0.1-config --bindir)/llvm-symbolizer -b ./sf -o aggregated.json
analyzer query aggregated.json -o whitelist.txt
make patched PERF=1
./patched small.yaml

## openssl
cp -r /home/zhongfa/SpecFuzz/example/usesec20/openssl/. /home/zhongfa/benchmarks/openssl-benchmark/openssl
mv /home/zhongfa/benchmarks/openssl-benchmark/openssl/Makefile /home/zhongfa/benchmarks/openssl-benchmark/
cd /home/zhongfa/benchmarks/openssl-benchmark
make sf SF_COLLECT=1
make clean
honggfuzz  --run_time 3600 --exit_upon_crash -Q --no_fb_timeout 1 --timeout 120 -n 1 -f ./ -l hongg.log -- ./sf ___FILE___ 2>&1 | analyzer collect -r hongg.log -o analyzer.json -b sf >errors.log 2>&1
analyzer minimize analyzer.json -o minimal.json
analyzer aggregate minimal.json -s $(llvm-7.0.1-config --bindir)/llvm-symbolizer -b ./sf -o aggregated.json
analyzer query aggregated.json -o whitelist.txt
make patched PERF=1
./patched speed -multi 4 rsa dsa ecdsa