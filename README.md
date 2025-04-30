# Embedded 11 - Secure Character Device with SHA-256 Encryption

This project implements a Linux kernel module named `chardev_crypto_module` that creates a character device (`/dev/char_dev`) with SHA-256 encryption functionality. It also demonstrates sending the encrypted output over an SSL connection to a Raspberry Pi running an OpenSSL server.

---

## 📦 Key Components

- **Kernel Module**: `chardev_crypto_module.c`
- **Device File**: `/dev/char_dev`
- **Hashing Algorithm**: SHA-256 via the Linux Kernel Crypto API
- **User-Space Communication**: via `echo` and `cat`
- **Secure Transmission**: using `openssl s_client` to send the hashed message over SSL

---

## 🔧 Module Features

- **Device Interface**:
  - `device_open`, `device_release`, `device_read`, `device_write`, `device_ioctl`
- **SHA-256 Hashing**:
  - Plaintext is written to the device and hashed using SHA-256.
  - The resulting hash is stored in a kernel buffer and can be read back.
- **Encryption Functionality**:
  - Implemented in `encrypt()`, using the Kernel Crypto API.
  - Output formatted as a hexadecimal string and displayed with `show_hash_result()`.

---

## 🖥️ Running the Module on Linux

### 1. Compile and Insert the Module
make && sudo insmod chardev_crypto_driver.ko

### 2. Write Data to the Device
echo "Hello from the user" > /dev/char_dev

### 3. Read the Hashed Output
cat /dev/char_dev

## 🔐 Sending the Hash to Raspberry Pi over SSL

You can send the hashed data to a Raspberry Pi using openssl s_client:

cat /dev/char_dev | openssl s_client -connect 192.168.254.152:44330

Replace 192.168.254.152 with your Raspberry Pi's IP address.

## 🍓 Raspberry Pi - OpenSSL Server Setup

### 1. Generate SSL Certificates
openssl req -new -newkey rsa:2048 -days 365 -nodes -x509 \
-keyout server.key -out server.crt

### 2. Start the SSL Server
openssl s_server -key server.key -cert server.crt -accept 44330

## 📡 Data Flow Diagram
The following diagram illustrates the interaction between the Linux kernel module, user space, and the Raspberry Pi over SSL:

- Linux User Space:

Action: Write plaintext to /dev/char_dev.

Action: Read the hashed output from /dev/char_dev.

- Linux Kernel Module:

Action: Receive data from user space.

Action: Perform SHA-256 hashing.

Action: Return the hashed data to user space.

- SSL Transmission:

Action: Send hashed data over SSL to Raspberry Pi.

- Raspberry Pi:

Action: Receive and display the hashed data.

Note: The diagram in files represents the flow of data from user space to the kernel module, where it is hashed, and then sent securely to the Raspberry Pi over SSL.


## 📌 Notes

Ensure kernel support for the Crypto API and SHA-256.

The character device must be created properly; check dmesg for logs.

Open port 44330 on the Raspberry Pi if needed.

You may use Wireshark to monitor encrypted communication (optional for debugging).

## ✅ Summary

This project demonstrates:

Linux kernel module development

Kernel-space encryption using SHA-256

Character device interaction from user space

Secure message transmission using OpenSSL over a custom port

