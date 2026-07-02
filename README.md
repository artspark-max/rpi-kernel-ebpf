# RPi6 Kernel with eBPF uprobe support

Custom Raspberry Pi kernel with **CONFIG_UPROBE_EVENTS=y** enabled for system-wide SSL/TLS plaintext interception using eBPF/bpftrace.

## What's different?

| Config | Stock kernel | This kernel |
|--------|:---:|:---:|
| CONFIG_UPROBE_EVENTS | ❌ disabled | ✅ enabled |
| CONFIG_BPF_EVENTS | ✅ enabled | ✅ enabled |
| CONFIG_KPROBE_EVENTS | ✅ enabled | ✅ enabled |
| CONFIG_HAVE_EBPF_JIT | ✅ enabled | ✅ enabled |

## Usage

1. **Trigger build** → Actions → "Build RPi6 Kernel with eBPF uprobe" → Run workflow
2. **Download** artifacts after build (~30 min)
3. **Install on RPi:**
```bash
# Extract
tar -xzf rpi-kernel-ebpf.tar.gz -C /
# Update boot config
echo "kernel=kernel8-ebpf.img" | sudo tee -a /boot/config.txt
# Reboot
sudo reboot
# Verify
cat /proc/config.gz | grep UPROBE_EVENTS
```

## Test eBPF after boot
```bash
# Install bpftrace
sudo apt install -y bpftrace

# Hook SSL_write in any process
sudo bpftrace -e 'uprobe:/usr/lib/aarch64-linux-gnu/libssl.so:SSL_write { printf("PID %d wrote %d bytes\n", pid, arg2); }'
```
