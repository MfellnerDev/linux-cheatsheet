# In short:

```shell
$ uname -r
$ sudo pacman -Q | grep linux

$ cd ~/Apps/wifi/rtw89
$ make clean
$ make
$ sudo make install
$ sudo modprobe --force-vermagic -v rtw_8852be
```

# Fix of WIFI-Drivers after Kernel update

My Laptop: Lenovo Thinkpad E14 Gen4 

Timestamp: 29122022T11.20

My Linux distribution: Manjaro KDE

My WiFi Device information (`inxi -n`): 
`Device-2: Realtek driver: rtw89_8852be
  IF: wlp3s0 state: up mac: f4:6a:dd:6d:9d:bd`
  
---
  
### Problem description

My Laptop is using the [rtw89_8852be driver](https://github.com/lwfinger/rtw89). Everything works perfect except after a Linux kernel update.

In this case, the kernel got updated from <u>5.15.84-1-MANJARO</u> to <u>5.15.85-1-MANJARO</u> and I had to rebuild the kernel modules for the WiFi drivers.

**"uname -r" output:** `5.15.85-1-MANJARO`


### Error description

Sounds easy enough, doesn't it? Just do a bunch of stuff to rebuild the modules and kick them up with `modprobe`. In reality, it wasn't that easy, sadly.

After I rebuild the kernel modules and tried to kick them up with `modprobe`, the following error came:
```shell
rtw_8852b: version magic '5.15.84-1-MANJARO SMP preempt mod_unload ' should be '5.15.85-1-MANJARO SMP preempt mod_unload '
```
(read kernel buffer logs with `dmesg -t > logfile.txt`)
The system detected a **version magic**, this means, that the modules are still build with the old kernel headers. This is stupid because that cannot be.


### Solution description

**! Note: This solution taints your Kernel. Using a tainted Kernel is not a big problem (as a "normal" Linux user). But it is still not the best solution to force load a Kernel module. You can read more about tainted Kernels here: [Tainted Kernels | docs.Kernel.org](https://docs.kernel.org/admin-guide/tainted-kernels.html)**

After all, the solution is simple but not really recommendable. I searched my entire system for the old kernel (or usages) with `sudo pacman -Q | grep linux`
and `uname -r` but they all showed only the latest kernel version.

So I just forced the versions to work:
```shell
$ sudo modprobe --force-vermagic -v rtw_8852be
```

The option `--force-vermagic` is the option that is """perfect""" for this problem. It forces the version magic to be okay.
In the end, `dmesg -t > logfile.txt` showed me the following which verifies my doing:

```shell
rtw89_8852be 0000:03:00.0: enabling device (0000 -> 0003)
rtw89_8852be 0000:03:00.0: Firmware version 0.27.32.1, cmd version 0, type 1
rtw89_8852be 0000:03:00.0: Firmware version 0.27.32.1, cmd version 0, type 3
rtw89_8852be 0000:03:00.0: chip rfe_type is 1
```

It works now. I am scared but my nerves are near doom.

**! This command uses the --force-vermagic option. You should NEVER force something. If you have to force somethin, it means that there is another way to do it. There has to be**

---

### Fazit

Yeah it works now and I can enjoy WiFi on my Laptop again. But I will certanly search for other solutions.
