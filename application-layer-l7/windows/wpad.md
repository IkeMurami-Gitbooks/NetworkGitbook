# WPAD

## About

Web Proxy Auto-Discovery Protocol

http://wpad/wpad.dat as PAC file

Hijack WPAD -> Proxy Server

Insert any html tags in HTTP Response

![](<../../.gitbook/assets/изображение (7).png>)

Атаке через протокол WPAD подвержены все системы — Linux, Windows, MacOS

Основная статья: [https://www.praetorian.com/blog/broadcast-name-resolution-poisoning-wpad-attack-vector/](https://www.praetorian.com/blog/broadcast-name-resolution-poisoning-wpad-attack-vector/)

## Impact

Злоумышленник может выполнить атаку «человек посередине» (MiTM) на уязвимые системы, если они находятся в той же локальной сети, что и система-жертва (внутренняя сеть, кафе, аэропорт).

Злоумышленник может выполнить атаку MiTM через Интернет, если он сможет зарегистрировать новый gTLD, который конфликтует с внутренней схемой имен, и развернуть поддельный прокси-сервер WPAD.

## Description

Есть разные варианты атаки, однако основная идея схожа. Атакующий отвечает на запросы аутентификации by spoofing name resolution responses. \[TODO]

## Example

Tools:\
Gladius: [https://www.praetorian.com/blog/gladius-automatic-responder-cracking](https://www.praetorian.com/blog/gladius-automatic-responder-cracking)\
HobORules: [https://www.praetorian.com/blog/hob064-statistics-based-password-cracking-rules-hashcat-d3adhob0](https://www.praetorian.com/blog/hob064-statistics-based-password-cracking-rules-hashcat-d3adhob0)\
Responder: [https://github.com/SpiderLabs/Responder](https://github.com/SpiderLabs/Responder)

Стартуем Gladius (чтобы он подхватывал хеши)

```
~/gladius# sudoo ./gladius.py --responder-ddir /home/ubuntu/Responder/logs -r hob064.rule
```

Далее запускаем Responder.

```
$ sudo responder -I eth2 -wF 
```

Далее заходим уже с полученными кредами по SMB, исполняем код, дампим ntds:

```
~/CrackMapExec# ./crackmapexec.py 10.10.10.2 -d CORP -u Administrator -p Password -x 'whoami'
~/CrackMapExec# ./crackmapexec.py 10.10.10.2 -d CORP -u Administrator -p Password --ntds drsuapi

```

Apart from getting the [NTLM hash to crack](https://zer1t0.gitlab.io/posts/attacking\_ad/#ntlm-hashes-cracking), this could be useful for [NTLM relay](https://zer1t0.gitlab.io/posts/attacking\_ad/#ntlm-relay) attacks, since the HTTP doesn't required sign in NTLM and therefore it can be used with any other protocol in NTLM cross-protocol relay attack.

Moreover, to serve the PAC file to the victim will allow you to execute some javascript code as the victim, which could be used to [exfiltrate the visited URLs](https://www.blackhat.com/docs/us-16/materials/us-16-Kotler-Crippling-HTTPS-With-Unholy-PAC.pdf).

## Защита

* Create a WPAD entry which points to the corporate proxy server or disable proxy auto-detection in Internet Explorer.
* Disable NBNS and LLMNR (test in a lab before deploying to all systems).
* Set valid DNS entries for all internal and external resources.
* Monitor the network for broadcast poisoning attacks.
* Restrict outbound 53/tcp and 445/tcp for all internal systems.
