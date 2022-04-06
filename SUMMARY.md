# Table of contents

* [Network Book](README.md)
* [Сети для самых маленьких](seti-dlya-samykh-malenkikh.md)

## Протоколы над сетями

* [NAT](protokoly-nad-setyami/nat.md)
* [BGP](protokoly-nad-setyami/bgp.md)
* [Криптографические протоколы](protokoly-nad-setyami/kriptograficheskie-protokoly/README.md)
  * [SSL](protokoly-nad-setyami/kriptograficheskie-protokoly/ssl.md)
  * [TLS](protokoly-nad-setyami/kriptograficheskie-protokoly/tls/README.md)
    * [Расширения](protokoly-nad-setyami/kriptograficheskie-protokoly/tls/rasshireniya/README.md)
      * [TLS-ALPN](protokoly-nad-setyami/kriptograficheskie-protokoly/tls/rasshireniya/tls-alpn.md)
  * [SSH](protokoly-nad-setyami/kriptograficheskie-protokoly/ssh.md)
  * [IPSec](protokoly-nad-setyami/kriptograficheskie-protokoly/ipsec.md)
* [Обмен сообщениями (IM) в режиме реального времени и информации о присутствии (Presence)](protokoly-nad-setyami/obmen-soobsheniyami-im-v-rezhime-realnogo-vremeni-i-informacii-o-prisutstvii-presence/README.md)
  * [SIMPLE (Основан на SIP)](protokoly-nad-setyami/obmen-soobsheniyami-im-v-rezhime-realnogo-vremeni-i-informacii-o-prisutstvii-presence/simple-osnovan-na-sip.md)
  * [XMPP/Jabber](protokoly-nad-setyami/obmen-soobsheniyami-im-v-rezhime-realnogo-vremeni-i-informacii-o-prisutstvii-presence/xmpp-jabber.md)
  * [Matrix](protokoly-nad-setyami/obmen-soobsheniyami-im-v-rezhime-realnogo-vremeni-i-informacii-o-prisutstvii-presence/matrix.md)

## Application layer (L7)

* [WEB](application-layer-l7/web/README.md)
  * [(не поддерживается) SPDY](application-layer-l7/web/ne-podderzhivaetsya-spdy.md)
  * [HTTP](application-layer-l7/web/http/README.md)
    * [WebSockets](application-layer-l7/web/http/websockets.md)
    * [h2c (HTTP/2 Cleartext)](application-layer-l7/web/http/h2c-http-2-cleartext.md)
    * [Коды ответов](application-layer-l7/web/http/kody-otvetov.md)
    * [Заголовки](application-layer-l7/web/http/zagolovki.md)
    * [URI](application-layer-l7/web/http/uri.md)
  * [HTTP/2](application-layer-l7/web/http-2.md)
  * [HTTP/3 (HTTP-over-QUIC)](application-layer-l7/web/http-3-http-over-quic.md)
* [Windows](application-layer-l7/windows/README.md)
  * [HTTP / Windows](application-layer-l7/windows/http-windows.md)
  * [LDAP](application-layer-l7/windows/ldap.md)
  * [RPC](application-layer-l7/windows/rpc.md)
  * [SMB](application-layer-l7/windows/smb.md)
  * [Windows Name Resolution](application-layer-l7/windows/windows-name-resolution/README.md)
    * [Какие есть](application-layer-l7/windows/windows-name-resolution/kakie-est.md)
    * [mDNS](application-layer-l7/windows/windows-name-resolution/mdns.md)
    * [LLMNR](application-layer-l7/windows/windows-name-resolution/llmnr.md)
    * [NBNS](application-layer-l7/windows/windows-name-resolution/nbns.md)
  * [WinRM](application-layer-l7/windows/winrm.md)
  * [WPAD](application-layer-l7/windows/wpad.md)
* [DHCP](application-layer-l7/dhcp.md)
* [NTP](application-layer-l7/ntp.md)
* [DNS](application-layer-l7/dns.md)
* [FTP](application-layer-l7/ftp.md)
* [SMTP](application-layer-l7/smtp.md)
* [SSH](application-layer-l7/ssh.md)
* [Telnet](application-layer-l7/telnet.md)
* [Почтовые протоколы](application-layer-l7/pochtovye-protokoly/README.md)
  * [IMAP](application-layer-l7/pochtovye-protokoly/imap.md)
  * [POP](application-layer-l7/pochtovye-protokoly/pop.md)

## Presentation Layer (L6)

* [SSL/TLS](presentation-layer-l6/ssl-tls.md)

## Session layer (L5)

* [SSDP](session-layer-l5/ssdp.md)

## Transport layer (L4)

* [QUIC](transport-layer-l4/quic.md)
* [UDP](transport-layer-l4/udp.md)
* [TCP](transport-layer-l4/tcp.md)

## Network layer (L3)

* [IP (IPv4, IPv6)](network-layer-l3/ip-ipv4-ipv6.md)
* [ICMP](network-layer-l3/icmp.md)

## Link layer (L2)

* [802.3 (Ethernet/ARP)](link-layer-l2/802.3-ethernet-arp.md)
* [802.1Q (VLANs)](link-layer-l2/802.1q-vlans.md)
* [802.11 (Wi-Fi)](link-layer-l2/802.11-wi-fi.md)

## Devices

* [Common Network Device Manufacturer](devices/common.md)
* [Physical Servers](devices/physical-servers.md)
