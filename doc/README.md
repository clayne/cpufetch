# cpufetch programming documentation (v0.88)
This documentation explains how cpufetch works internally and all the design decisions I made. The intention of this documentation is to be useful for me in the future, for everyone interested in the project, and for anyone who is trying to obtain any specific information from the CPU. In this way, this can be used as a manual or a page which collects interesting material in this area.

### 1. Basics
cpufetch works for __x86_64__ CPUs (Intel and AMD) and experimental support for __ARM__. Other kinds of x86_64 CPU are not supported (I don't think supporting other CPUs may pay off). Depending on the architecture, cpufetch choose certain files to be compiled. A summarized tree of the source code of cpufetch is shown below.

```
cpufetch/
├── DOCUMENTATION.md
├── Makefile
├── README.md
└── src/
    ├── arm/
    │   ├── midr.c
    │   ├── midr.h
    │   └── other files ...
    ├── common/
    │   └── common files ...
    └── x86/
        ├── cpuid.c
        ├── cpuid.h
        └── other files ...
```

Source code is divided in three directories:

- `common/`: Source code shared beteen x86 and ARM
- `arm/`: ARM dependant source code
- `x86/`: x86_64 dependant source code

##### 1.1 Basics (x86_64)

In x86, __cpufetch works using the CPUID instruction__. It is called directly using assembly (see `src/x86/cpuid_asm.c`). To understand how CPUID works you may have a look at the [Useful documentation](https://github.com/Dr-Noob/cpufetch/blob/master/DOCUMENTATION_x86.md#useful-documentation) section (more precisely, Link 1).

At the beginning of execution, cpufetch needs to know the max standard CPUID level and max CPUID extended level supported in the running CPU. We also need to know if the x86 CPU is Intel or AMD because some fetching depends on it. This information will be stored in:

```
struct cpuInfo {
  ...
  VENDOR cpu_vendor;
  uint32_t maxLevels;  
  uint32_t maxExtendedLevels;
  ...
};
```

To use any CPUID leaf, we always need to check that it is supported in the current CPU. cpufetch will always check if the leaf is supported in the current CPU, and will use a different workaround if the CPU is Intel or AMD, which leads us to the following section.

##### 1.2 Basics (ARM)
In ARM, __cpufetch works using the MIDR register and Linux filesystem__. MIDR (Main ID Register) is fetched from `/proc/cpuinfo`. It allows the detection of the microarchitecture of the cores. Furthermore, Linux filesystem `/sys/devices/system/cpu/` is used to fetch the number of cores, and other information. This is the main reason to explain __why `cpufetch` only works on Linux kernel based systems.__

##### 1.3 Documentation organization
The rest of the documentation is divided into x86 and ARM architectures, since each one need different implementations:

- [DOCUMENTATION_X86.md](https://github.com/Dr-Noob/cpufetch/blob/master/doc/DOCUMENTATION_X86.md)
- [DOCUMENTATION_ARM.md](https://github.com/Dr-Noob/cpufetch/blob/master/doc/DOCUMENTATION_ARM.md)