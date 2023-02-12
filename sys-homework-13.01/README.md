# `Домашнее задание к занятию 13.1 "Уязвимости и атаки на информационные системы"` - `Болдыш Леонид`

### Задание 1

-`Сетевые службы: ftp, ssh, telnet, smtp, http, mysql, postgresql, nfs и др.`
- `Уязвимости:`
1. `MySQL 5.0.75 - 'sql_parse.cc' Multiple Format String Vulnerabilities`
2. `vsftpd 2.3.4 - Backdoor Command Execution`
3. `TelnetD encrypt_keyid - Function Pointer Overwrite`
4. `Postfix SMTP 4.2.x < 4.2.48 - 'Shellshock' Remote Command Injection`
5. `ISC BIND 9 - TKEY Remote Denial of Service (PoC)`
6. `RPCBind / libtirpc - Denial of Service`

### Задание 2

- `Анализ с точки зрения сетевого трафика:`
1. `В режиме SYN флаги RST, ACK. RST бит установлен в 1.`
2. `В режиме FIN флаг RST в 1 бит`
3. `За В режиме флагами RST и ACK следует FIN, PSH, URG.`

- `Как отвечает сервер на запросы при данных режимах:`