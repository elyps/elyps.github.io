Hier ist ein Beispiel für eine **Docker Compose**-Datei, um **WireGuard** in einem Container zu betreiben.

### Voraussetzungen

- **Docker** und **Docker Compose** sind installiert.
- Ein Netzwerkinterface mit öffentlicher IP-Adresse für den WireGuard-Server ist verfügbar.

### `docker-compose.yml`

version: '3.8'

services:
  wireguard:
    image: linuxserver/wireguard
    container_name: wireguard
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    environment:
      - PUID=1000 # Passe die UID deines Users an
      - PGID=1000 # Passe die GID deines Users an
      - TZ=Europe/Berlin # Setze deine Zeitzone
      - SERVERURL=your-domain.com # Domain oder öffentliche IP des Servers
      - SERVERPORT=51820 # Standard-Port für WireGuard
      - PEERS=5 # Anzahl der Client-Konfigurationen, die generiert werden sollen
      - PEERDNS=1.1.1.1 # DNS-Server für die Clients
    volumes:
      - ./config:/config # Persistente Speicherung der WireGuard-Daten
      - /lib/modules:/lib/modules
    ports:
      - 51820:51820/udp # Exponiere den WireGuard-Port
    sysctls:
      - net.ipv4.conf.all.src_valid_mark=1
    restart: unless-stopped

### Anleitung zur Einrichtung

1. **Compose-Datei erstellen**  
    Speichere die oben genannte Konfiguration in einer Datei namens `docker-compose.yml`.
    
2. **Konfiguration des Servers starten**  
    Starte den WireGuard-Server mit:
    
    ```bash
    docker-compose up -d
    ```
    
3. **Client-Konfigurationen abrufen**  
    Nach dem Start werden die Client-Konfigurationsdateien unter `./config/peerX/peerX.conf` gespeichert. Diese Dateien kannst du direkt in die WireGuard-Apps auf den Clients importieren.
    
4. **Firewall anpassen**  
    Stelle sicher, dass der Port `51820/udp` in deiner Firewall geöffnet ist:
    
    ```bash
    sudo ufw allow 51820/udp
    ```
    
5. **WireGuard testen**  
    Verbinde dich mit einem der Clients und überprüfe, ob die Verbindung hergestellt wird.
    

### Anpassungen

- Ändere die **PUID** und **PGID** je nach Benutzerberechtigungen.
- Passe `SERVERURL` an deine öffentliche IP oder Domain an.
- Nutze ein anderes Verzeichnis als `./config` für die Konfigurationsspeicherung, falls nötig.

Damit hast du eine funktionierende Docker Compose-Konfiguration für einen WireGuard-Server.


## Fehlerbehebungen

### [TUN] [wireguard_pc] Handshake for peer 1 (104.21.64.1:51820) did not complete after 5 seconds, retrying (try 2)

This log entry indicates that WireGuard attempted a handshake with the peer at 104.21.64.1:51820, but the process did not complete within the expected 5-second window, so it’s automatically retrying (now on try 2). A handshake is essential for establishing the encrypted tunnel, and its failure may be due to one or more of the following issues:

1. **Network Connectivity**:
    
    - The remote peer might be unreachable due to network outages or high latency.
    - Intermediate firewalls or NAT devices could be blocking or delaying UDP packets on port 51820.
2. **Configuration Issues**:
    
    - Mismatched or incorrect public/private keys between peers.
    - Incorrect allowed IPs or endpoint settings in the WireGuard configuration.
3. **Firewall or NAT Restrictions**:
    
    - UDP port 51820 may not be open on one or both sides.
    - NAT traversal issues might be preventing the handshake packets from reaching the destination.

#### Troubleshooting Steps

4. **Verify Configuration**
    
    - **Local and Remote Configurations**: Ensure that both sides have matching public keys, correct endpoint addresses, and accurate allowed IP ranges.
    - Example configuration snippet:
        
[Interface]
PrivateKey = <your_private_key>
Address = 10.0.0.1/24

[Peer]
PublicKey = <peer_public_key>
AllowedIPs = 10.0.0.2/32
Endpoint = 104.21.64.1:51820
PersistentKeepalive = 25

Use the `PersistentKeepalive` option if one peer is behind NAT to keep the connection active.
1. **Check Firewall and NAT Settings**
    
    - Ensure that UDP port 51820 is allowed on both local and remote firewalls.
    - If behind a router or NAT device, verify that port forwarding is correctly configured to route UDP traffic to the WireGuard service.
2. **Examine Logs and Connection Status**
    
    - Use the command `wg show` to inspect handshake timestamps and transfer statistics. This can help identify if the handshake is repeatedly timing out.
    - Check system logs on both peers for any additional error messages.
3. **Test Network Reachability**
    
    - Verify that the remote peer is reachable by testing UDP connectivity. For example, use tools like `nmap` or `nc` (netcat) to check if the port is open:
        
        ```bash
        nc -vz -u 104.21.64.1 51820
        ```
        
        Note that UDP testing might not provide definitive results due to its connectionless nature, but it can still help diagnose if packets are being dropped.
4. **Restart Services if Necessary**
    
    - If changes are made or if the issue persists, restarting the WireGuard service on both ends can sometimes help:
        
        ```bash
        sudo systemctl restart wg-quick@wg0
        ```
        

By methodically checking these areas, you can narrow down the cause of the handshake timeout and address the underlying issue.
