In diesem Tutorial beschäftige ich mich diesmal mit AdGuard Home, welches in einer virtuellen Debian 11 Installation läuft. Des Weiteren kommt ein eigener rekursiver DNS-Server (hier Unbound) zum Einsatz.

## Was ist Unbound

Unbound ist ein validierender, rekursiver, zwischenspeichernder DNS-Revolver. Er ist auf Schnelligkeit und Schlankheit ausgelegt und enthält moderne, auf offenen Standards basierende Funktionen. Ende 2019 wurde Unbound einer strengen Prüfung unterzogen, was bedeutet, dass die Codebasis stabiler denn je ist.

**Voraussetzungen:**  
**-** Eingerichteter [Virtual Machine Manager (VMM)](https://foxly.de/article/53-virtual-machine-manager-vmm-einrichten/)

## Einrichtung einer Debian 11 VM

Die Einrichtung einer virtuellen Maschine im Synology VMM gestaltet sich genauso simpel wie in jedem anderen Virtualisierungsprogramm. Damit dieses Tutorial allerdings nicht zu lang wird, habe ich hierfür ein Video erstellt, wie eine VM eingerichtet wird. Auch die Grundeinstellungen unter Debian, dies beinhaltet eine statische Netzwerkeinstellung sowie die Erreichbarkeit via SSH unter Port 22. Eine explizite Härtung des Systems nehme ich hier nicht vor.

Externer Inhalt [youtu.be](https://youtu.be/W0xfHRZ_Erk)

Inhalte von externen Seiten werden ohne Ihre Zustimmung nicht automatisch geladen und angezeigt.

Durch die Aktivierung der externen Inhalte erklären Sie sich damit einverstanden, dass personenbezogene Daten an Drittplattformen übermittelt werden. Mehr Informationen dazu haben wir in unserer Datenschutzerklärung zur Verfügung gestellt.

**Die Gasterweiterung sollte in jedem Fall noch installiert werden!**

## Installation AdGuard Home & Unbound

### AdGuard Home

Die Installation ohne Docker ist fast genauso schnell wie mit. Auf [GitHub](https://github.com/AdguardTeam/AdGuardHome#getting-started) befindet sich das AdGuard Repository. Darin findet sich in der Dokumentation ein automatisiertes Script zur Installation. Dieses muss lediglich ausgeführt werden um AdGuard Home zu installieren.

Code: install.sh

curl -s -S -L https://raw.githubusercontent.com/AdguardTeam/AdGuardHome/master/scripts/install.sh | sh -s -- -v

Nach der Installation kann wie gewohnt die Oberfläche unter <IP-Adresse>:3000 aufgerufen werden. Hier angekommen, erfolgt die Ersteinrichtung von AdGuard. Wählen Sie also ein Netzwerk Interface aus und erstellen Sie Ihr Administratorkonto. Nun sollten Sie auch schon in der regulären Arbeitsoberfläche des AdGuard sein. Sollte hier ein Update verfügbar sein, können Sie dieses starten.

#### Konfiguration/Einrichtung

Damit AdGuard auch Ihren eigenen DNS-Server (Unbound) nutzt, müssen Sie unter _**Einstellungen > DNS-Einstellungen > Upstream-DNS-Server**_ folgendes eingetragen werden:

Code

´´´
127.0.0.1:5335
´´´

Wenn Sie eine Namensauflösung der verbundenen Clients bevorzugen, müssen Sie unter _**Private inverse DNS-Server**_, Ihre IP-Adresse des DHCP-Servers angeben.

Sie sollten auch den _Begrenzungswert_ unter DNS-Serverkonfiguration auf 0 setzen oder an einen angepassten Wert nach Ihrem Nutzungsverhalten.

Sämtliche weiteren Einstellungen können Sie frei nach Ihrem Belieben wählen.

### Unbound

Auch Unbound ist kein Hexenwerk zu installieren und konfigurieren. Führen Sie folgende Befehle aus.

Code

sudo apt install unbound

Damit die root DNS-Server auch auf den aktuellsten Stand sind, laden Sie diese bitte herunter.

Code

wget https://www.internic.net/domain/named.root -qO- | sudo tee /var/lib/unbound/root.hints

Jetzt erstellen Sie mit folgendem Befehl eine Datei unter /etc/unbound/unbound.conf.d/ und füllen Sie mit unten angegeben Inhalt. Lassen Sie sich bitte nicht beirren, folgende Config ist aus der Pi-hole Dokumentation. Sie funktioniert sowohl mit Pi-hole als auch mit AdGuard Home. Des Weiteren ist die Konfigurationsdatei für Unbound so, wie sie ist, gut und kann eins zu eins übernommen werden.


nano /etc/unbound/unbound.conf.d/pi-hole.conf


server:
    # If no logfile is specified, syslog is used
    # logfile: "/var/log/unbound/unbound.log"
    verbosity: 0

    interface: 127.0.0.1
    port: 5335
    do-ip4: yes
    do-udp: yes
    do-tcp: yes

    # May be set to yes if you have IPv6 connectivity
    do-ip6: no

    # You want to leave this to no unless you have *native* IPv6. With 6to4 and
    # Terredo tunnels your web browser should favor IPv4 for the same reasons
    prefer-ip6: no

    # Use this only when you downloaded the list of primary root servers!
    # If you use the default dns-root-data package, unbound will find it automatically
    #root-hints: "/var/lib/unbound/root.hints"

    # Trust glue only if it is within the server's authority
    harden-glue: yes

    # Require DNSSEC data for trust-anchored zones, if such data is absent, the zone becomes BOGUS
    harden-dnssec-stripped: yes

    # Don't use Capitalization randomization as it known to cause DNSSEC issues sometimes
    # see https://discourse.pi-hole.net/t/unbound-stubby-or-dnscrypt-proxy/9378 for further details
    use-caps-for-id: no

    # Reduce EDNS reassembly buffer size.
    # IP fragmentation is unreliable on the Internet today, and can cause
    # transmission failures when large DNS messages are sent via UDP. Even
    # when fragmentation does work, it may not be secure; it is theoretically
    # possible to spoof parts of a fragmented DNS message, without easy
    # detection at the receiving end. Recently, there was an excellent study
    # >>> Defragmenting DNS - Determining the optimal maximum UDP response size for DNS <<<
    # by Axel Koolhaas, and Tjeerd Slokker (https://indico.dns-oarc.net/event/36/contributions/776/)
    # in collaboration with NLnet Labs explored DNS using real world data from the
    # the RIPE Atlas probes and the researchers suggested different values for
    # IPv4 and IPv6 and in different scenarios. They advise that servers should
    # be configured to limit DNS messages sent over UDP to a size that will not
    # trigger fragmentation on typical network links. DNS servers can switch
    # from UDP to TCP when a DNS response is too big to fit in this limited
    # buffer size. This value has also been suggested in DNS Flag Day 2020.
    edns-buffer-size: 1232

    # Perform prefetching of close to expired message cache entries
    # This only applies to domains that have been frequently queried
    prefetch: yes

    # One thread should be sufficient, can be increased on beefy machines. In reality for most users running on small networks or on a single machine, it should be unnecessary to seek performance enhancement by increasing num-threads above 1.
    num-threads: 1

    # Ensure kernel buffer is large enough to not lose messages in traffic spikes
    so-rcvbuf: 1m

    # Ensure privacy of local IP ranges
    private-address: 192.168.0.0/16
    private-address: 169.254.0.0/16
    private-address: 172.16.0.0/12
    private-address: 10.0.0.0/8
    private-address: fd00::/8
    private-address: fe80::/10



Nachdem Sie die Konfigurationsdatei erstellt haben, muss Unbound neu gestartet werden, damit die Änderungen in Kraft treten.

Code

sudo service unbound restart

Testen Sie Ihren lokalen DNS-Server.

Code

dig foxly.de @127.0.0.1 -p 5335

Die erste Anfrage über den eigenen DNS-Server dauert eine Weile, da die Root-DNS-Server abgefragt werden, im Anschluss legt Unbound die Abfrage/Antwort in den Cache und die Seiten werden blitzschnell angezeigt.

## Test

Auf der Seite [canyoublockit.com](https://canyoublockit.com/) können Sie testen, ob alles funktioniert.

## Troubleshooting

Sollten Sie beim Absenden einiger Befehle auf einen Fehler stoßen, ist i. d. R. das Paket lokal nicht vorhanden, welches hierfür benutzt wird. Dies können Sie unkompliziert beheben.

Code

apt update && apt upgrade -y && apt install curl wget sudo net-tools nano -y

[AdguardTeam/AdGuardHome: Network-wide ads & trackers blocking DNS server (github.com)](https://github.com/AdguardTeam/AdGuardHome#getting-started)
