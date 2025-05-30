---
title: 'NahamCon CTF 2025'
date: 2025-05-03 21:00:00 +0700
categories: [ctf]
tags: [Web,Crypto]     
published: true
description: "WU của mình cho giải NahamCon CTF 2025"
---

# Web 


# Crypto
## Cryptoclock
Soucre code của bài 
```python
#!/usr/bin/env python3
import socket
import threading
import time
import random
import os
from typing import Optional

def encrypt(data: bytes, key: bytes) -> bytes:
    """Encrypt data using XOR with the given key."""
    return bytes(a ^ b for a, b in zip(data, key))

def generate_key(length: int, seed: Optional[float] = None) -> bytes:
    """Generate a random key of given length using the provided seed."""
    if seed is not None:
        random.seed(int(seed))
    return bytes(random.randint(0, 255) for _ in range(length))

def handle_client(client_socket: socket.socket):
    """Handle individual client connections."""
    try:
        with open('flag.txt', 'rb') as f:
            flag = f.read().strip()
        
        current_time = int(time.time())
        key = generate_key(len(flag), current_time)
        
        encrypted_flag = encrypt(flag, key)
        
        welcome_msg = b"Welcome to Cryptoclock!\n"
        welcome_msg += b"The encrypted flag is: " + encrypted_flag.hex().encode() + b"\n"
        welcome_msg += b"Enter text to encrypt (or 'quit' to exit):\n"
        client_socket.send(welcome_msg)
        
        while True:
            data = client_socket.recv(1024).strip()
            if not data:
                break
                
            if data.lower() == b'quit':
                break
                
            key = generate_key(len(data), current_time)
            encrypted_data = encrypt(data, key)
            
            response = b"Encrypted: " + encrypted_data.hex().encode() + b"\n"
            client_socket.send(response)
            
    except Exception as e:
        print(f"Error handling client: {e}")
    finally:
        client_socket.close()

def main():
    server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    server.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
    
    server.bind(('0.0.0.0', 1337))
    server.listen(5)
    
    print("Server started on port 1337...")
    
    try:
        while True:
            client_socket, addr = server.accept()
            print(f"Accepted connection from {addr}")
            client_thread = threading.Thread(target=handle_client, args=(client_socket,))
            client_thread.start()
    except KeyboardInterrupt:
        print("\nShutting down server...")
    finally:
        server.close()

if __name__ == "__main__":
    main() 

```

Để ý key được tạo bởi hàm `key = generate_key(len(flag), current_time)`. Seed của hàm random chính là `current_time = int(time.time())`. Do time được giữ cố định trong mỗi lần ta gọi lên server nên việc mình cần làm chỉ đơn giản là gửi tới server `b'\x00' * flag_len` thì server sẽ trả về key và lấy key này xor với enc_flag là được

Script:
```python
from pwn import *
HOST = 'challenge.nahamcon.com'
PORT = 30272
def xor_bytes(a: bytes, b: bytes) -> bytes:
    return bytes(x ^ y for x, y in zip(a, b))
r = remote(HOST,PORT)
def main():
    io = remote(HOST, PORT)
    response = io.recvuntil(b"Enter text to encrypt")
    print(response.decode())
    for line in response.decode().splitlines():
        if "The encrypted flag is:" in line:
            enc_flag_hex = line.split(": ")[1]
            break
    enc_flag = bytes.fromhex(enc_flag_hex)
    flag_len = len(enc_flag)
    io.sendline(b'\x00' * flag_len)
    while True:
        response = io.recvline()
        if b"Encrypted: " in response:
            key_hex = response.decode().strip().split("Encrypted: ")[1]
            break
    key = bytes.fromhex(key_hex)
    flag = xor_bytes(enc_flag, key)
    print("[+] Flag:", flag.decode())
if __name__ == "__main__":
    main()
```
