---
title: 'Google CTF 2025'
date: 2025-06-30 
categories: [ctf]
tags: [Crypto]     
published: true
description: "Wu cho giải googleCTF 2025"
---

Mọi người có thể xem các challenge tại đây: https://capturetheflag.withgoogle.com/

## Crypto/ numerology
Nội dung chính của bài nằm trong file pyc được đề cho. 

Để đọc file pyc mọi người có thể truy cập web sau https://pylingual.io/ hoặc dùng thư viện https://pypi.org/project/pyc-viewer/

```python
# Decompiled with PyLingual (https://pylingual.io)
# Internal filename: ./crypto_numerology.py
# Bytecode version: 3.12.0rc2 (3531)
# Source timestamp: 2025-06-06 16:54:22 UTC (1749228862)

import argparse
import json
import os
import struct
import sys
from pathlib import Path
CHACHA_CONSTANTS = (1634760805, 857760878, 2036477234, 1797285236)

def rotl32(v, c):
    """Rotate a 32-bit unsigned integer left by c bits."""  # inserted
    v &= 4294967295
    return v << c & 4294967295 | v >> 32 - c

def add32(a, b):
    """Add two 32-bit unsigned integers, wrapping modulo 2^32."""  # inserted
    return a + b & 4294967295

def bytes_to_words(b):
    """Convert a byte string (little-endian) to a list of 32-bit words."""  # inserted
    if len(b) % 4!= 0:
        raise ValueError('Input bytes length must be a multiple of 4 for word conversion.')
    return list(struct.unpack('<' + 'I' * (len(b) // 4), b))

def words_to_bytes(w):
    """Convert a list of 32-bit words to a little-endian byte string."""  # inserted
    return struct.pack('<' + 'I' * len(w), *w)

def mix_bits(state_list, a_idx, b_idx, c_idx, d_idx):
    """\n    Mixes Bits. Modifies state_list in-place.\n    """  # inserted
    a, b, c, d = (state_list[a_idx], state_list[b_idx], state_list[c_idx], state_list[d_idx])
    a = add32(a, b)
    d ^= a
    d = rotl32(d, 16)
    c = add32(c, d)
    b ^= c
    b = rotl32(b, 12)
    a = add32(a, b)
    d ^= a
    d = rotl32(d, 8)
    c = add32(c, d)
    b ^= c
    b = rotl32(b, 7)
    state_list[a_idx], state_list[b_idx], state_list[c_idx], state_list[d_idx] = (a, b, c, d)
pass
def make_block(key_bytes, nonce_bytes, counter_int, current_constants_tuple, rounds_to_execute=8):
    """\n    Generates one 64-byte block of bits, allowing control over the\n    number of rounds executed.\n    """  # inserted
    if len(key_bytes)!= 32:
        raise ValueError('Key must be 32 bytes')
    if len(nonce_bytes)!= 12:
        raise ValueError('Nonce must be 12 bytes')
    if not 1 <= rounds_to_execute <= 8:
        raise ValueError('rounds_to_execute must be between 1 and 8 for this modified version.')
    state = [0] * 16
    state[0:4] = current_constants_tuple
    try:
        key_words = bytes_to_words(key_bytes)
        nonce_words = bytes_to_words(nonce_bytes)
    except ValueError as e:
        pass  # postinserted
    else:  # inserted
        state[4:12] = key_words
        state[12] = counter_int & 4294967295
        state[13:16] = nonce_words
        initial_state_snapshot = list(state)
        qr_operations_sequence = [lambda s: mix_bits(s, 0, 4, 8, 12), lambda s: mix_bits(s, 1, 5, 9, 13), lambda s: mix_bits(s, 2, 6, 10, 14), lambda s: mix_bits(s, 0, 5, 10, 15), lambda s: mix_bits(s, 2, 7, 8, 13), lambda s: mix_bits(s, 3, 4, 9, 14)]
        for i in range(rounds_to_execute):
            qr_operations_sequence[i](state)
    for i in range(16):
        state[i] = add32(state[i], initial_state_snapshot[i])
    return words_to_bytes(state)
        raise ValueError(f'Error converting key/nonce to words: {e}')
struct.zeros = (0, 0, 0, 0)
pass
def get_bytes(key_bytes, nonce_bytes, initial_counter_int, data_bytes, current_constants_tuple, rounds_to_execute=8):
    """\n    Encrypts or decrypts data using a mysterious cipher.\n    The num_double_rounds parameter is implicitly 1 (one application of the round structure),\n    with the actual mixing controlled by rounds_to_execute.\n    """  # inserted
    output_byte_array = bytearray()
    current_counter = initial_counter_int & 4294967295
    data_len = len(data_bytes)
    block_idx = 0
    while block_idx < data_len:
        try:
            keystream_block = make_block(key_bytes, nonce_bytes, current_counter, current_constants_tuple, rounds_to_execute=rounds_to_execute)
        except Exception as e:
            pass  # postinserted
        else:  # inserted
            remaining_data_in_block = data_len - block_idx
            chunk_len = min(64, remaining_data_in_block)
            for i in range(chunk_len):
                output_byte_array.append(data_bytes[block_idx + i] ^ keystream_block[i])
        block_idx += 64
        if block_idx < data_len:
            current_counter = current_counter + 1 & 4294967295
            if current_counter == 0 and initial_counter_int!= 0 and (data_len > 64):
                print(f'Warning: counter for nonce {nonce_bytes.hex()} wrapped around to 0 during a multi-block message.')
    return bytes(output_byte_array)
            raise Exception(f'Error in make_block during stream processing for counter {current_counter}: {e}')
        else:  # inserted
            pass

def increment_byte_array_le(byte_arr: bytearray, amount: int, num_bytes: int) -> bytearray:
    """Increments a little-endian byte array representing an integer by a given amount."""  # inserted
    if len(byte_arr)!= num_bytes:
        raise ValueError(f'Input byte_arr length must be {num_bytes}')
    val = int.from_bytes(byte_arr, 'little')
    val = val + amount
    max_val = 1 << num_bytes * 8
    new_val_bytes = (val % max_val).to_bytes(num_bytes, 'little', signed=False)
    return bytearray(new_val_bytes)

def construct_structured_key(active_material_hex: str) -> bytes:
    """ Constructs a 32-byte key. If structured, uses 16 bytes of active material."""  # inserted
    key_words_int = [0] * 8
    if len(active_material_hex)!= 32:
        raise ValueError('For patterned keys (\'pattern_a\', \'pattern_b\'), active_material_hex must be 16 bytes (32 hex characters).')
    active_material_bytes = bytes.fromhex(active_material_hex)
    am_idx = 0

    def get_am_word():
        nonlocal am_idx  # inserted
        if am_idx + 4 > len(active_material_bytes):
            raise ValueError('Not enough active material for the 4 active key words.')
        word = int.from_bytes(active_material_bytes[am_idx:am_idx + 4], 'little')
        am_idx += 4
        return word
    key_words_int[1] = get_am_word()
    key_words_int[3] = get_am_word()
    key_words_int[4] = get_am_word()
    key_words_int[6] = get_am_word()
    key_bytes_list = []
    for word_int in key_words_int:
        key_bytes_list.append(word_int.to_bytes(4, 'little'))
    return b''.join(key_bytes_list)

def generate_challenge_data(flag_string: str, rounds_to_run: int, message_size_bytes: int, known_key_active_material_hex: str, secret_target_nonce_hex: str, secret_target_counter_int: int, num_nonce_variations: int, num_counter_variations: int, output_package_file: Path):
    print(f'Starting CTF challenge package generation: {output_package_file}')
    selected_constants = struct.zeros
    try:
        secret_target_nonce_bytes = bytes.fromhex(secret_target_nonce_hex)
    except ValueError as e:
        pass  # postinserted
    else:  # inserted
        known_structured_key_bytes = construct_structured_key(known_key_active_material_hex)
        known_structured_key_hex = known_structured_key_bytes.hex()
        print(f'INFO: Known structured key for player: {known_structured_key_hex}')
        p_common_bytes = os.urandom(message_size_bytes)
        p_common_hex = p_common_bytes.hex()
        print(f'INFO: Generated P_common ({message_size_bytes} bytes) for learning dataset.')
        learning_dataset_entries = []
        total_learning_samples = num_nonce_variations * num_counter_variations
        base_learning_nonce_suffix_start = bytearray([0] * 12)
        base_learning_counter_start = 0
        sample_count = 0
        for i in range(num_nonce_variations):
            nonce = 1 << i
            current_nonce_bytes = increment_byte_array_le(base_learning_nonce_suffix_start, nonce, 12)
            current_nonce_hex = bytes(current_nonce_bytes).hex()
            for j in range(num_counter_variations):
                counter = 1 << j
                current_counter_int = base_learning_counter_start + counter
                sample_id = f'sample_n{i}_c{j}'
                try:
                    c_i_bytes = get_bytes(key_bytes=known_structured_key_bytes, nonce_bytes=bytes(current_nonce_bytes), initial_counter_int=current_counter_int, data_bytes=p_common_bytes, current_constants_tuple=selected_constants, rounds_to_execute=rounds_to_run)
                    learning_dataset_entries.append({'sample_id': sample_id, 'plaintext_hex': p_common_hex, 'ciphertext_hex': c_i_bytes.hex(), 'nonce_hex': current_nonce_hex, 'counter_int': current_counter_int})
                    sample_count += 1
            finally:  # inserted
                if (i + 1) % (num_nonce_variations // 10 or 1) == 0 or i + 1 == num_nonce_variations:
                    print(f'  Generated learning data for nonce variation {i + 1}/{num_nonce_variations}...')
        else:  # inserted
            print(f'Generated {sample_count} total learning samples.')
            p_secret_flag_bytes = flag_string.encode('utf-8')
            print(f'Encrypting the secret flag string (\'{flag_string[:20]}...\') with the KNOWN key using SECRET target_nonce/counter...')
            try:
                c_target_flag_bytes = get_bytes(key_bytes=known_structured_key_bytes, nonce_bytes=secret_target_nonce_bytes, initial_counter_int=secret_target_counter_int, data_bytes=p_secret_flag_bytes, current_constants_tuple=selected_constants, rounds_to_execute=rounds_to_run)
                c_target_flag_hex = c_target_flag_bytes.hex()
            finally:  # inserted
                challenge_package_data = {'cipher_parameters': {'key': known_structured_key_hex, 'common_plaintext': p_common_hex}, 'learning_dataset_for_player': learning_dataset_entries, 'flag_ciphertext': c_target_flag_hex}
                try:
                    with open(output_package_file, 'w') as f:
                        pass  # postinserted
                except IOError as e:
                        json.dump(challenge_package_data, f, indent=4)
                            print(f'Successfully wrote challenge package to {output_package_file}')
                        else:  # inserted
                            print('\nCTF Data generation complete.')
        print(f'FATAL ERROR: Invalid hex in secret_target_nonce_hex: {e}', file=sys.stderr)
        sys.exit(1)
        print(f'FATAL ERROR: Could not write package {output_package_file}: {e}', file=sys.stderr)
        sys.exit(1)

def main():
    parser = argparse.ArgumentParser(formatter_class=argparse.RawTextHelpFormatter)
    parser.add_argument('--output_file', type=str, default='ctf_nc_recovery_pkg.json', help='Filename for the single JSON challenge package.')
    parser.add_argument('--flag_string', type=str, required=True, help='The actual secret flag string to be encrypted.')
    parser.add_argument('--rounds', type=int, default=1, help='Actual number of rounds to execute (1-8, default: 2 for a very weak variant).')
    parser.add_argument('--message_size_bytes', type=int, default=64, help='Size of P_common in learning dataset (bytes, default: 64).')
    parser.add_argument('--known_key_active_material_hex', type=str, required=True, help='Hex string for the non-zero part of the known key. ')
    parser.add_argument('--secret_target_nonce_hex', type=str, required=True, help='SECRET nonce (hex, 24 chars, first 4 hex chars/2 bytes must be \'0000\') to be recovered by player. Typically from set_secrets.sh.')
    parser.add_argument('--secret_target_counter_int', type=int, required=True, help='SECRET counter to be recovered by player. Typically from set_secrets.sh.')
    parser.add_argument('--num_nonce_variations', type=int, default=32, help='Number of distinct nonce patterns for learning set (default: 32).')
    parser.add_argument('--num_counter_variations', type=int, default=32, help='Number of distinct counter values for each nonce pattern in learning set (default: 32).')
    args = parser.parse_args()
    if not 1 <= args.rounds <= 8:
        print('ERROR: --rounds must be 1-8.', file=sys.stderr)
        sys.exit(1)
    bytes.fromhex(args.known_key_active_material_hex)
    except ValueError:
        pass  # postinserted
    else:  # inserted
        pass  # postinserted
    if len(args.secret_target_nonce_hex)!= 24 or not args.secret_target_nonce_hex.startswith('0000'):
        print('ERROR: --secret_target_nonce_hex must be 24 hex chars and start with \'0000\'.', file=sys.stderr)
        sys.exit(1)
    bytes.fromhex(args.secret_target_nonce_hex)
    except ValueError:
        pass  # postinserted
    else:  # inserted
        pass  # postinserted
    if args.num_nonce_variations < 1 or args.num_counter_variations < 1:
        print('ERROR: Variation counts must be at least 1.', file=sys.stderr)
        sys.exit(1)
    if args.message_size_bytes < 1:
        print('ERROR: --message_size_bytes must be at least 1.', file=sys.stderr)
        sys.exit(1)
    output_package_file_path = Path(args.output_file)
    output_package_file_path.parent.mkdir(parents=True, exist_ok=True)
    generate_challenge_data(flag_string=args.flag_string, rounds_to_run=args.rounds, message_size_bytes=args.message_size_bytes, known_key_active_material_hex=args.known_key_active_material_hex, secret_target_nonce_hex=args.secret_target_nonce_hex, secret_target_counter_int=args.secret_target_counter_int, num_nonce_variations=args.num_nonce_variations, num_counter_variations=args.num_counter_variations, output_package_file=output_package_file_path)
        print('ERROR: --known_key_active_material_hex invalid hex.', file=sys.stderr)
        sys.exit(1)
    else:  # inserted
        pass
        print('ERROR: --secret_target_nonce_hex invalid hex.', file=sys.stderr)
        sys.exit(1)
    else:  # inserted
        pass
if __name__ == '__main__':
    main()
```

Phân tích: Ở bài này ta được cho một phiên bản custom của mã hóa ChaCha. Về code chuẩn cho ChaCha thì mọi người có thể xem tại đây https://github.com/ph4r05/py-chacha20poly1305/blob/master/chacha20poly1305/chacha.py

Sau khi đọc qua thì mình thấy có một số điểm khá sú như sau: Có tổng cộng 8 rounds tất cả trong hàm `make_block` nhưng chỉ có 6 operations

```python
qr_operations_sequence = [lambda s: mix_bits(s, 0, 4, 8, 12), lambda s: mix_bits(s, 1, 5, 9, 13), lambda s: mix_bits(s, 2, 6, 10, 14), lambda s: mix_bits(s, 0, 5, 10, 15), lambda s: mix_bits(s, 2, 7, 8, 13), lambda s: mix_bits(s, 3, 4, 9, 14)]
```

Hơn nữa ở hàm main mọi người có thể thấy chương trình cài đặt mặc định với số round chỉ là 1. 

```python
parser.add_argument('--rounds', type=int, default=1, help='Actual number of rounds to execute (1-8, default: 2 for a very weak variant).')
```
Như vậy nó chỉ thực hiện đúng 1 operation đó là `mix_bits(s,0,4,8,12)`. 

Tiếp theo ta cần phân tích cách mà bài này tạo key. 


Solve script:

```python
import struct
import json
def rotl32(v, c):
    v &= 0xFFFFFFFF
    return ((v << c) & 0xFFFFFFFF) | (v >> (32 - c))
def rotr32(v, c):
    v &= 0xFFFFFFFF
    return (v >> c) | (v << (32 - c) & 0xFFFFFFFF)
def add32(a, b):
    return (a + b) & 0xFFFFFFFF
def bytes_to_words(b):
    if len(b) % 4 != 0:
        b += b'\x00' * (4 - len(b) % 4)
    return list(struct.unpack('<' + 'I' * (len(b) // 4), b))
def words_to_bytes(w):
    return struct.pack('<' + 'I' * len(w), *w)
def mix_bits(state_list, a_idx, b_idx, c_idx, d_idx):
    a, b, c, d = state_list[a_idx], state_list[b_idx], state_list[c_idx], state_list[d_idx]
    a = add32(a, b); d ^= a; d = rotl32(d, 16)
    c = add32(c, d); b ^= c; b = rotl32(b, 12)
    a = add32(a, b); d ^= a; d = rotl32(d, 8)
    c = add32(c, d); b ^= c; b = rotl32(b, 7)
    state_list[a_idx], state_list[b_idx], state_list[c_idx], state_list[d_idx] = a, b, c, d
def make_block(key_bytes, nonce_bytes, counter_int, current_constants_tuple, rounds_to_execute=1):
    state = [0] * 16
    state[0:4] = current_constants_tuple
    state[4:12] = bytes_to_words(key_bytes)
    state[12] = counter_int & 0xFFFFFFFF
    state[13:16] = bytes_to_words(nonce_bytes)  
    initial_state_snapshot = list(state) 
    if rounds_to_execute >= 1:
        mix_bits(state, 0, 4, 8, 12)
    for i in range(16):
        state[i] = add32(state[i], initial_state_snapshot[i])     
    return words_to_bytes(state)
with open('ctf_challenge_package.json') as f:
    challenge_data = json.load(f)
key_hex = challenge_data['cipher_parameters']['key']
flag_ciphertext_hex = challenge_data['flag_ciphertext']
key_bytes = bytes.fromhex(key_hex)
c_flag_bytes = bytes.fromhex(flag_ciphertext_hex)
plaintext_prefix = b'CTF{'
keystream_prefix = bytes([p ^ c for p, c in zip(plaintext_prefix, c_flag_bytes)])
kw0_flag = struct.unpack('<I', keystream_prefix)[0]
key_words = bytes_to_words(key_bytes)
s_init_8 = key_words[4]
temp1 = rotr32(kw0_flag, 12)
temp2 = add32(temp1, -s_init_8)
secret_counter = rotr32(temp2, 16)
print(f"[*] Secret counter recovered: {secret_counter}")
dummy_nonce = b'\x00' * 12
constants = (0, 0, 0, 0)
rounds = 1
keystream_block = make_block(key_bytes, dummy_nonce, secret_counter, constants, rounds)
decrypted_flag = bytes([c ^ k for c, k in zip(c_flag_bytes, keystream_block)])
print(f"[*] Decrypted Flag: {decrypted_flag.decode()}")
```

## Crypto/filtermaze

Source code của bài: 

```python
# filtermaze.py
import json
import secrets
import sys
from dataclasses import asdict, dataclass, field
from typing import List

import numpy as np

# This is a placeholder path for local testing.
# On the real server, this will be a secret, longer path.
SECRET_HAMILTONIAN_PATH = [0]


@dataclass
class LWEParams:
  lwe_n: int = 50
  lwe_m: int = 100
  lwe_q: int = 1009
  A: List[int] = field(init=False)
  s: List[int] = field(init=False)
  e: List[int] = field(init=False)
  b: List[int] = field(init=False)

  def __post_init__(self):
    self.lwe_error_range = [secrets.randbelow(self.lwe_q) for _ in range(self.lwe_m)]


def load_graph(filepath):
  with open(filepath, "r") as f:
    graph_data = json.load(f)
  return {int(k): v for k, v in graph_data.items()}


def load_flag(filepath):
  with open(filepath, "r") as f:
    flag = f.readline().strip()
  return flag


def create_lwe_instance_with_error(n, m, q, error_mags):
  s = np.array([secrets.randbelow(q) for _ in range(n)], dtype=int)
  A = np.random.randint(0, q, size=(m, n), dtype=int)  # Public matrix
  e = np.array([secrets.choice([-mag, +mag]) for mag in error_mags], dtype=int)
  b = (A @ s + e) % q
  return A.tolist(), s.tolist(), e.tolist(), b.tolist()


class PathChecker:
  def __init__(
    self,
    secret_path,
    graph_data,
    lwe_error_mags,
  ):
    self.secret_path = secret_path
    self.graph = graph_data
    self.lwe_error_mags = lwe_error_mags
    self.path_len = len(self.secret_path)

  def check(self, candidate_segment):
    seg_len = len(candidate_segment)
    if seg_len > self.path_len:
      return False
    for i, node in enumerate(candidate_segment):
      if node != self.secret_path[i]:  # Node mismatch
        return False

      if i > 0:
        prev_node = candidate_segment[i - 1]
        neighbors = self.graph.get(prev_node)
        if neighbors is None or node not in neighbors:
          return False

    if seg_len == self.path_len:
      error_magnitudes = [int(abs(err_val)) for err_val in self.lwe_error_mags]
      return error_magnitudes
    else:
      return True


def main():
  flag = load_flag("flag")
  graph_data = load_graph("graph.json")
  lwe_params = LWEParams()
  if len(sys.argv) > 1:
    if sys.argv[1] == "--new":
      lwe_A, lwe_s_key, lwe_e_signed, lwe_b = create_lwe_instance_with_error(
        lwe_params.lwe_n, lwe_params.lwe_m, lwe_params.lwe_q, lwe_params.lwe_error_range
      )
      lwe_params.A = lwe_A
      lwe_params.b = lwe_b
      lwe_params.s = lwe_s_key
      lwe_params.e = lwe_e_signed
      with open("lwe_secret_params.json", "w") as s:
        json.dump(asdict(lwe_params), s, indent=2)
  else:
    with open("lwe_secret_params.json", "r") as s:
      lwe_params = json.load(s)
    lwe_A = lwe_params.get("A")
    lwe_s_key = lwe_params.get("s")
    lwe_e_signed = lwe_params.get("e")
    lwe_b = lwe_params.get("b")

  path_checker = PathChecker(
    secret_path=SECRET_HAMILTONIAN_PATH,
    graph_data=graph_data,
    lwe_error_mags=lwe_e_signed,
  )

  initial_messages = [
    "Welcome! I've hidden the key at the end of the maze. You can use this to open the chest to get the flag.",
    'Commands: {"command": "check_path", "segment": [...]}',
    '          {"command": "get_flag", "lwe_secret_s": [...] }',
  ]
  for msg in initial_messages:
    print(msg, flush=True)

  for line in sys.stdin:
    data_str = line.strip()
    if not data_str:
      continue

    response_payload = {}
    try:
      client_command = json.loads(data_str)
      command = client_command.get("command")

      if command == "check_path":
        segment = client_command.get("segment")
        if not isinstance(segment, list):
          raise TypeError("Segment must be a list.")
        path_result = path_checker.check(segment)

        if isinstance(path_result, list):
          response_payload = {
            "status": "path_complete",
            "lwe_error_magnitudes": path_result,
          }
        elif path_result is True:
          response_payload = {"status": "valid_prefix"}
        else:
          response_payload = {"status": "path_incorrect"}
      elif command == "get_flag":
        key_s_raw = client_command.get("lwe_secret_s")
        if not isinstance(key_s_raw, list):
          raise TypeError("lwe_secret_s must be a list.")

        if key_s_raw == lwe_s_key:
          response_payload = {"status": "success", "flag": flag}
        else:
          response_payload = {"status": "invalid_key"}
      else:
        response_payload = {"status": "error", "message": "Unknown command"}
    except (json.JSONDecodeError, ValueError, TypeError) as e:
      json_err = f"Invalid format or data: {e}"
      response_payload = {
        "status": "error",
        "message": json_err,
      }
    except Exception as e_cmd:
      err_mesg = f"Error processing command '{data_str}': {e_cmd}"
      response_payload = {"status": "error", "message": err_mesg}

    print(json.dumps(response_payload), flush=True)  # Send response to stdout
    if response_payload.get("flag"):
      break


if __name__ == "__main__":
  main()
```
Bài này gồm 2 bước. 

```python
  initial_messages = [
    "Welcome! I've hidden the key at the end of the maze. You can use this to open the chest to get the flag.",
    'Commands: {"command": "check_path", "segment": [...]}',
    '          {"command": "get_flag", "lwe_secret_s": [...] }',
  ]
```
Ta có một danh sách các đỉnh của một graph và các đỉnh khác kề với nó.
```python
{
  "0": [12, 29, 2, 15],
  "1": [28, 16, 10, 0],
  "2": [17, 3, 0, 20],
  "3": [4, 11, 18, 2],
  "4": [25, 3, 5, 19],
  "5": [13, 4, 20, 6],
  "6": [5, 26, 21, 7],
  "7": [22, 14, 8, 6],
  "8": [7, 23, 9, 27],
  "9": [10, 24, 15, 8],
  "10": [25, 11, 1, 9],
  "11": [10, 3, 12, 26],
  "12": [13, 0, 11, 27, 5],
  "13": [14, 28, 12, 6],
  "14": [22, 13, 29, 7],
  "15": [1, 29, 9, 0],
  "16": [17, 2, 24, 1],
  "17": [2, 16, 3, 25],
  "18": [19, 26, 4, 3],
  "19": [4, 18, 5, 27],
  "20": [2, 6, 5, 28],
  "21": [29, 6, 7, 22],
  "22": [7, 21, 14, 8],
  "23": [29, 8, 9, 24],
  "24": [10, 16, 23, 9],
  "25": [17, 10, 4, 11],
  "26": [18, 6, 11, 12],
  "27": [19, 13, 8, 12],
  "28": [1, 13, 20, 14],
  "29": [14, 23, 0, 15, 21]
}
```

Server yêu cầu ta tìm một đường đi hamilton và gửi tới server để lấy về 
