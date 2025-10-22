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

---

Would you like me to also make a small `manage-wifi.sh` script that runs these three modes interactively (`shutdown`, `clean`, `activate`) via simple arguments?
