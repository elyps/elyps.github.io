**Schritt-für-Schritt: AdGuard Home in einer Proxmox-VM installieren und konfigurieren**

---

## 1. Voraussetzungen

- Proxmox VE mit GUI- oder CLI-Zugriff
    
- Fritzbox als Router im LAN-Netz 192.168.178.0/24
    
- ISO-Image einer Linux-Distribution (z. B. Debian 12 oder Ubuntu 22.04) im Proxmox-Storage
    
- Freie IPv4-Adresse im LAN, z. B. **192.168.178.60**
    

---

## 2. ISO ins Proxmox-Storage laden

1. Via CLI auf Proxmox-Host:
    
    ```bash
    # Beispiel: Upload Debian-12 ISO ins “local” Storage
    pvesm upload local /pfad/zur/debian-12.iso iso
    ```
    
2. In der GUI:
    
    - Datacenter → Storage “local” → ISO-Image → **Upload** → ISO auswählen → **Upload**.
        

---

## 3. VM anlegen

### 3.1 VM-Grundkonfiguration (CLI)

# VM-ID 102, Name “adguard-vm”
qm create 102 \
  --name adguard-vm \
  --memory 2048 \
  --cores 2 \
  --net0 virtio,bridge=vmbr0 \
  --bootdisk scsi0 \
  --scsihw virtio-scsi-pci \
  --ostype l26

### 3.2 Festplatte und CD-ROM

# Festplatte (20 GB) im local-lvm
qm set 102 --scsi0 local-lvm:20
# Debian-ISO als CD-ROM mounten
qm set 102 --ide2 local:iso/debian-12.iso,media=cdrom
# Boot-Reihenfolge: zuerst CD-ROM, dann Festplatte
qm set 102 --boot order=ide2;scsi0

---

## 4. OS-Installation

1. VM starten und Konsole öffnen:
    
    ```bash
    qm start 102
    qm console 102
    ```
    
2. Debian/Ubuntu Setup durchlaufen:
    
    - Sprache, Tastaturlayout wählen
        
    - Hostname: **adguard-vm**
        
    - Netzwerk: **manuell** → IP `192.168.178.60/24`, Gateway `192.168.178.1`, DNS Server während Installation z. B. `1.1.1.1`
        
    - Partitionierung: ganze Platte (LVM oder Standard)
        
    - Benutzer anlegen (z. B. `admin`)
        
    - OpenSSH Server installieren
        
    - Abschluss
        
3. CD-ROM aushängen (CLI auf Proxmox):
    
    ```bash
    qm set 102 --ide2 none,media=cdrom
    ```
    

---

## 5. Erstzugriff und Basis-Setup

Per SSH in die VM (als root oder `sudo`-User):

ssh admin@192.168.178.60
sudo -i

# System updaten
apt update && apt upgrade -y
apt install -y curl wget tar
# Hostname (falls nötig) und Zeitzone prüfen
hostnamectl set-hostname adguard-vm
timedatectl set-timezone Europe/Berlin

---

## 6. AdGuard Home installieren

1. **Herunterladen** der aktuellen Release:
    
    ```bash
    cd /tmp
    curl -sSL \
      https://github.com/AdguardTeam/AdGuardHome/releases/latest/download/AdGuardHome_linux_amd64.tar.gz \
      -o AdGuardHome.tar.gz
    ```
    
2. **Entpacken & Installation**:
    
    ```bash
    tar xzf AdGuardHome.tar.gz
    cd AdGuardHome
    ./AdGuardHome -s install
    ```
    
    - Installiert unter `/opt/AdGuardHome`
        
    - Systemd-Service `AdGuardHome.service` wird eingerichtet
        
3. **Service prüfen**:
    
    ```bash
    systemctl enable --now AdGuardHome
    systemctl status AdGuardHome
    ```
    

---

## 7. Ersteinrichtung via Web-UI

Im Browser aufrufen:

http://192.168.178.60:3000

1. **Admin-Account** anlegen
    
2. **Upstream-DNS-Server** konfigurieren (z. B. 1.1.1.1, 8.8.8.8)
    
3. DNS-Port 53 (Standard) bestätigen
    
4. Optional: DoH/DoT aktivieren
    
5. Setup abschließen → Web-UI ab sofort unter 3000, DNS-Service auf 53
    

---

## 8. Fritzbox: DNS-Anfragen umleiten

1. Fritzbox-Webinterface öffnen (`http://fritz.box`)
    
2. **Internet → Zugangsdaten → DNS-Server**
    
3. „Andere DNSv4-Server verwenden“ aktivieren
    
4. Eintragen:
    
    - **Primär:** `192.168.178.60`
        
    - **Sekundär:** z. B. `1.1.1.1` (Fallback)
        
5. Einstellungen übernehmen → Neustart der Fritzbox
    

_Alle per DHCP angeschlossenen Geräte fragen nun AdGuard Home an._

---

## 9. Abschließende Tests und Feinschliff

- **DNS-Test** von einem Client oder in der VM:
    
    ```bash
    dig @192.168.178.60 example.com +short
    ```
    
- **Filterlisten** in AdGuard-UI unter **Filters → DNS-Filter** hinzufügen
    
- **Lokales DHCP** (optional):
    
    - Fritzbox-DHCP deaktivieren
        
    - AdGuard Home → **Settings → DHCP** aktivieren, Subnetz `/24` konfigurieren
        

---

Deine AdGuard Home-VM ist nun betriebs­bereit: volle Isolation, einfache Wartung und zuverlässiges DNS-Blocking für dein gesamtes Netzwerk.
