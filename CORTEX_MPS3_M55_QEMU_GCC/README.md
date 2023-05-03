
# Blinky example on QEMU Armv8-M Cortex-M55
The example supports two configurations, namely blinky demo `BLINKY_DEMO` and full demo `FULL_DEMO`. The blinky demo prints a message to the console when a receiver task receives an expected message from the sender task. The full demo implements soak testing of FreeRTOS kernel on QEMU Cortex-M55.

# Prerequisites

## QEMU
There is bug in SysTick timer implementation which is fixed in [latest version](https://gitlab.com/qemu-project/qemu/-/commit/77cd997). Therefore, it is recommended to build QEMU from source, rather than using the version from `apt get`.

### Building QEMU from source
Run the following commands to build QEMU from source:
```bash
git clone https://gitlab.com/qemu-project/qemu.git
cd qemu
git submodule update --init --recursive
mkdir build && cd build
../configure --enable-debug  --target-list=arm-softmmu,aarch64-softmmu
make
```

If the build is successful, then `qemu-system-arm` will be created in the `build` folder.

**NOTE**  
Make sure that the path to QEMU executable `qemu-system-arm` is available in the `PATH` variable.

## Build tools
* [CMake](https://cmake.org/download/)
* [GNU Arm Embedded Toolchain](https://developer.arm.com/downloads/-/arm-gnu-toolchain-downloads)

# The blinky example
The blinky example uses `CMake` as the build system and supports two configurations, blinky demo `BLINKY_DEMO` and full demo `FULL_DEMO`. Run the following command to clone FreeRTOS repository:

```bash
git clone https://github.com/FreeRTOS/FreeRTOS.git --recurse-submodules
```

## Building and running the blinky demo
### Building the blinky demo
Run the following commands to build the blinky demo:

```bash
cd FreeRTOS/FreeRTOS/Demo/ThirdParty/Partner-Supported-Demos/CORTEX_MPS3_M55_QEMU_GCC
rm -rf build && cmake -B build -DBLINKY_DEMO=1 --toolchain=gcc_toolchain.cmake . && cmake --build build
```

### Running the demo
Run the following command to execute the blinky demo:
```bash
qemu-system-arm -machine mps3-an547 -serial stdio -monitor null -nographic -d cpu_reset,guest_errors -device loader,file=./build/cortex_mps3_m55_qemu_example
```

### Expected output
```bash
CPU Reset (CPU 0)
R00=00000000 R01=00000000 R02=00000000 R03=00000000
R04=00000000 R05=00000000 R06=00000000 R07=00000000
R08=00000000 R09=00000000 R10=00000000 R11=00000000
R12=00000000 R13=00000000 R14=00000000 R15=00000000
XPSR=40000000 -Z-- A NS priv-thread
Invalid read at addr 0x0, size 4, region '(null)', reason: rejected
Invalid read at addr 0x4, size 4, region '(null)', reason: rejected
CPU Reset (CPU 0)
R00=00000000 R01=00000000 R02=00000000 R03=00000000
R04=00000000 R05=00000000 R06=00000000 R07=00000000
R08=00000000 R09=00000000 R10=00000000 R11=00000000
R12=00000000 R13=00000000 R14=ffffffff R15=00000000
XPSR=40000000 -Z-- A S priv-thread
Received something
blinking
Received something
blinking
Received something
blinking
Received something
blinking
Received something
blinking
Received something
blinking
```

### Building the full demo
Run the following commands to build the full demo:

```bash
cd FreeRTOS/FreeRTOS/Demo/ThirdParty/Partner-Supported-Demos/CORTEX_MPS3_M55_QEMU_GCC
rm -rf build && cmake -B build -DFULL_DEMO=1 --toolchain=gcc_toolchain.cmake . && cmake --build build
```

### Running the full demo
Run the following command to execute the full demo:
```bash
qemu-system-arm -machine mps3-an547 -serial stdio -monitor null -nographic -d cpu_reset,guest_errors -device loader,file=./build/cortex_mps3_m55_qemu_example
```

### Expected output
```bash
CPU Reset (CPU 0)
R00=00000000 R01=00000000 R02=00000000 R03=00000000
R04=00000000 R05=00000000 R06=00000000 R07=00000000
R08=00000000 R09=00000000 R10=00000000 R11=00000000
R12=00000000 R13=00000000 R14=00000000 R15=00000000
XPSR=40000000 -Z-- A NS priv-thread
Invalid read at addr 0x0, size 4, region '(null)', reason: rejected
Invalid read at addr 0x4, size 4, region '(null)', reason: rejected
CPU Reset (CPU 0)
R00=00000000 R01=00000000 R02=00000000 R03=00000000
R04=00000000 R05=00000000 R06=00000000 R07=00000000
R08=00000000 R09=00000000 R10=00000000 R11=00000000
R12=00000000 R13=00000000 R14=ffffffff R15=00000000
XPSR=40000000 -Z-- A S priv-thread
OK: No errors - tick count 10013 
OK: No errors - tick count 20011 
OK: No errors - tick count 30010 
```
