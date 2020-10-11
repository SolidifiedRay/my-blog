---
title: Cracking Passwords with John the Ripper
date: 2020-10-11
categories:
- Notes
---

Learn how to crack hash files with John the Ripper  

John the Ripper is an open source password-cracking tools. John the Ripper can uses various options to crack passowrds using the `--format` option, such as MD5, SHA1, SHA256, and so on. John the Ripper can also use `--wordlist` option to compare the hashes against. If the password can be find within the wordlist, the speed of cracking the password will increase. One of the popular wordlist is ['rockyou.txt'](https://github.com/brannondorsey/naive-hashcat/releases/download/data/rockyou.txt)  

For example, if you want to crack a file named 'md5.txt', and the wordlist is in 'usr/share/wordlist', you can run the following command:  
`john --wordlist=/usr/share/wordlist/rockyou.txt --format=raw-md5 md5.txt`  

You can run `john --help` for more command line options.  


\* You can practise John the Ripper in [Immersive Labs](https://dca.immersivelabs.online/labs/introduction-to-john-the-ripper/)  