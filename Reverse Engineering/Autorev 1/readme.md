# Autorev 1

> Description: You think you can reverse engineer? Let's test out your speed
Connect to the service! Here's the command: nc mysterious-sea.picoctf.net 54077.
>
> Author: SkrubLawd
>
> Handouts: 

`Flag: picoCTF{4u7o_r3v_g0_brrr_78c345aa}`

## Solution

1.Connect the netcat:`nc mysterious-sea.picoctf.net 57062`

2.The server reponse:
```diff
$ nc mysterious-sea.picoctf.net 57062
Welcome! I think I'm pretty good at reverse enginnering. There's NO WAY anyone's better than me. Wanna try? I have 20 binaries I'm going to send you and you have 1 second EACH to get the secret in each one. Good luck >:)
+'975091324'
Here's the next binary in bytes:
7f454c4602010100000000000000000002003e000100000050104000000000004000000000000000283900000000000000000000400038000d00400020001f000600000004000000400000000000000040004000000000004000400000000000d802000000000000d802000000000000080000000000000003000000040000001803000000000000180340000000000018034000000000001c000000000000001c00000000000000010000000000000001000000040000000000000000000000000040000000000000004000000000006805000000000000680500000000000000100000000000000100000005000000001000000000000000104000000000000010400000000000a101000000000000a10100000000000000100000000000000100000004000000002000000000000000204000000000000020400000000000f400000000000000f40000000000000000100000000000000100000006000000f82d000000000000f83d400000000000f83d4000000000001c02000000000000200200000000000000100000000000000200000006000000082e000000000000083e400000000000083e400000000000d001000000000000d001000000000000080000000000000004000000040000003......0000000000
What's the secret?:
```
Bonus: ELF(Executable and Linkable Format) is the format file of Linux.It's start with 7f 45 4c 46 (45 4c 46 is E L F).

3.We can see the strange number:
```
Welcome! I think I'm pretty good at reverse enginnering. There's NO WAY anyone's better than me. Wanna try? I have 20 binaries I'm going to send you and you have 1 second EACH to get the secret in each one. Good luck >:)
`975091324`
Here's the next binary in bytes:
```
4.Send `975091324` to this response secret key.

5.The server response `Correct!` then ask a next secret key?
```
What's the secret?:975091324
Correct!
52861511
Here's the next binary in bytes:
7f454c4602010100000000000000000002003e000100000050104000000000004000000000000000283900000000000000000000400038000d00400020001f000600000004000000400000000000000040004000000000004000400000000000d802000000000000d8020000000000000800000000000000030000000400000018030000000...
```
6.Thus, I relize the solve is pwn script.Then, I write the python scripyt with 20 loop that recive the flag in the last response.
``` 
from pwn import *

r = remote('mysterious-sea.picoctf.net',57062)

r.recvuntil(b">:)\n")
key = r.recvline().strip()

r.recvuntil(b"What's the secret?:")
r.sendline(key)

for i in range (2,21):
    r.recvuntil(b"Correct!\n")
    key=r.recvline().strip()

    r.recvuntil(b"What's the secret?:")
    r.sendline(key)
r.interactive()
```
7.Running the script will automate all 20 rounds and print the flag:
```
python3 autorev1.py
[+] Opening connection to mysterious-sea.picoctf.net on port 56082: Done
[*] Switching to interactive mode
Correct!
Woah, how'd you do that??
Here's your flag: picoCTF{4u7o_r3v_g0_brrr_78c345aa}
```