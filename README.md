### 🧭 **README.md — Manage and Control Wi-Fi Interface (`wlp2s0`)**

#### ⚙️ Overview

This guide documents how to **disable**, **clean**, and **reactivate** the Wi-Fi interface `wlp2s0`.
Useful when you have multiple Wi-Fi adapters (e.g., keeping only `wlxd0374599f0a2` active).

---

### 🔻 **1. Shut down `wlp2s0`**

Turn off the interface and make NetworkManager ignore it.

```bash
# Tell NetworkManager to stop managing wlp2s0
sudo nmcli device set wlp2s0 managed no

# Bring the interface down (radio off)
sudo ip link set wlp2s0 down
```

✅ Result:

* Interface is inactive (`state DOWN`)
* NetworkManager ignores it (`unmanaged`)

Check:

```bash
nmcli device status
```

---

### 🧹 **2. Clean / Flush any existing IP address**

Remove any leftover IP assignments from previous connections.

```bash
sudo ip addr flush dev wlp2s0
```

✅ Result:

* No `inet` address will appear under `ip a show wlp2s0`

Check:

```bash
ip a show wlp2s0
```

---

### 🔼 **3. Reactivate `wlp2s0`**

Bring the interface back to life and let NetworkManager control it again.

```bash
# Allow NetworkManager to manage the device again
sudo nmcli device set wlp2s0 managed yes

# Bring it up
sudo ip link set wlp2s0 up
```

✅ Result:

* NetworkManager can reconnect normally
* `nmcli device status` shows `available` or `connected`

---

### 🔁 **Quick Summary**

| Action               | Command Sequence                                                          |
| :------------------- | :------------------------------------------------------------------------ |
| **Shut Down**        | `sudo nmcli device set wlp2s0 managed no && sudo ip link set wlp2s0 down` |
| **Clean / Flush IP** | `sudo ip addr flush dev wlp2s0`                                           |
| **Reactivate**       | `sudo nmcli device set wlp2s0 managed yes && sudo ip link set wlp2s0 up`  |

---

### 🧩 Optional: Verify

```bash
nmcli device status
ip a show wlp2s0
```

### Likewise LAN

```bash
sudo nmcli connection add type ethernet ifname enp3s0 con-name enp3s0 ipv4.method auto ipv6.method ignore
sudo nmcli connection up enp3s0
```

### 🧩 What each part does:

* `type ethernet` → tells NetworkManager it’s a wired connection
* `ifname enp3s0` → specifies the exact interface name
* `con-name enp3s0` → names the connection profile (you’ll see this under `nmcli connection show`)
* `ipv4.method auto` → enables DHCP (automatic IP from router)
* `ipv6.method ignore` → skips IPv6 (optional but avoids delays)
* `connection up` → activates the connection immediately

---

After running that, your system got the proper IP:

```
inet 192.168.1.159/24 brd 192.168.1.255 scope global dynamic noprefixroute enp3s0
```

✅ That’s the command sequence that fixed the `169.254.x.x` self-assigned IP and brought your LAN online.

