**Proxmox Backup Server (PBS)** is an enterprise-grade, open-source backup solution designed to back up and restore virtual machines (VMs), containers, and physical hosts. It integrates seamlessly with **Proxmox Virtual Environment (PVE)** and provides deduplication, compression, encryption, and fast incremental backups.

## **Key Features of Proxmox Backup Server**

1. **Efficient Backup & Restore**
    
    - Supports **incremental backups**, reducing storage usage.
    - **Deduplication** ensures that only unique data is stored, minimizing space.
    - **Zstandard compression** optimizes storage without excessive CPU overhead.
2. **Strong Encryption**
    
    - Uses **AES-256-GCM encryption** to secure backup data.
    - Client-side encryption ensures that backups remain secure even on untrusted storage.
3. **Fast Restore**
    
    - Single-file restore for virtual machines.
    - **Live restore** allows users to start a VM while the restore process is still running.
4. **Web-based Management Interface**
    
    - Modern web UI with real-time monitoring and reporting.
    - API for automation and integration with other systems.
5. **Storage Flexibility**
    
    - Supports **local disks, NAS, SAN, and remote storage** (via NFS, CIFS, or Object Storage like S3).
    - Can be used with **ZFS for enhanced reliability**.
6. **Incremental Backups with Proxmox VE**
    
    - Uses **QEMU dirty bitmap tracking** to send only changed data since the last backup.
    - Allows frequent backups without performance overhead.
7. **Snapshot & File-Level Restore**
    
    - Supports restoring entire virtual machines or individual files from backups.
    - Backup images can be mounted for direct file access.
8. **Retention Policies & Pruning**
    
    - Configurable rules to automatically remove old backups.
    - Keeps only the necessary snapshots based on time-based policies.
9. **CLI & API Integration**
    
    - **Command-line tools** for automation and scripting.
    - **REST API** allows integration into other infrastructure management systems.

## **Installation of Proxmox Backup Server**

### **1. Download ISO & Install**

- Get the latest ISO from [Proxmox](https://www.proxmox.com/en/downloads)
- Boot from the ISO and follow the installation wizard.

### **2. Update the System**

After installation, log in and update:

apt update && apt full-upgrade -y

### **3. Configure Storage for Backups**

If using an additional disk, initialize it with:

lsblk  # Identify the disk (e.g., /dev/sdb)
fdisk /dev/sdb  # Create a partition
mkfs.ext4 /dev/sdb1  # Format it
mkdir /mnt/backup
mount /dev/sdb1 /mnt/backup

To make it persistent, add to `/etc/fstab`:

/dev/sdb1 /mnt/backup ext4 defaults 0 2

### **4. Create a Datastore**

proxmox-backup-manager datastore create my-backups /mnt/backup

### **5. Configure Proxmox VE for Backups**

In **Proxmox VE Web UI**:

- Go to **Datacenter → Storage → Add → Proxmox Backup Server**.
- Enter PBS details (IP, username, and password).
- Select the **datastore** created earlier.

### **6. Perform a Backup**

In Proxmox VE:

- Go to **VM → Backup** and create a new backup job.
- Choose **Proxmox Backup Server** as the target.

### **7. Restore a Backup**

- Navigate to **Datacenter → Backup**.
- Select a VM/Container and click **Restore**.

## **Advanced Features**

### **Remote Backup**

To back up a remote PVE server, add PBS as a target using `proxmox-backup-client`:

proxmox-backup-client backup root.pxar:/ --repository root@pam@192.168.1.100:my-backups

### **Pruning & Garbage Collection**

To remove old backups:

proxmox-backup-manager prune my-backups --keep-last 5 --keep-daily 7 --keep-weekly 4
proxmox-backup-manager garbage-collect

## **Conclusion**

Proxmox Backup Server is a powerful and efficient backup solution for **Proxmox VE environments**, offering **incremental backups, deduplication, encryption, and fast restores**. It’s a must-have for enterprise-grade VM backups while remaining open-source and free to use.




## FAQ
### Kann ich ein Backup das ich mit dem proxmox Backup server von meinen containern erstellt habe, in einen anderen frisch installierten proxmox backup Server importieren und wiederherstellen?

Ja, du kannst ein Backup, das mit dem **Proxmox Backup Server (PBS)** erstellt wurde, in einen frisch installierten **Proxmox Backup Server** importieren und wiederherstellen. Hier ist der Ablauf:

### **1. Backup auf den neuen PBS übertragen**

Falls dein Backup auf einem externen Speichermedium liegt (z. B. eine externe Festplatte oder ein Netzwerkspeicher), musst du es zuerst auf den neuen PBS kopieren.

#### **Variante 1: Direktes Kopieren vom alten PBS**

Falls du den alten PBS noch hast, kannst du das Backup mit `rsync` oder `scp` auf den neuen Server übertragen:

rsync -avz /pfad/zum/backup root@neuerpbs:/pfad/wo/es/hin/soll

oder mit `scp`:

scp -r /pfad/zum/backup root@neuerpbs:/pfad/wo/es/hin/soll

Falls das Backup auf einem NAS liegt, kannst du es direkt von dort mounten.

#### **Variante 2: Backup von externem Medium kopieren**

Falls du das Backup auf einer externen Festplatte hast:

1. **Mounten** der Festplatte auf dem neuen PBS:
    
    ```bash
    mount /dev/sdX /mnt/externe-platte
    ```
    
2. **Kopieren** der Daten:
    
    ```bash
    cp -r /mnt/externe-platte/backup /pfad/wo/es/hin/soll
    ```
    

### **2. Backup-Speicher in PBS einbinden**

PBS speichert Backups in **Datastores**. Falls der Speicherort nicht bereits als **Datastore** konfiguriert ist, musst du ihn hinzufügen:

1. **In der PBS-Weboberfläche einloggen** (`https://neuerpbs:8007`)
2. Unter **Datastore** → **Add** einen neuen Datastore erstellen, der auf das Backup-Verzeichnis zeigt.

Alternativ kannst du dies per CLI machen:

proxmox-backup-manager datastore create <Name> --path /pfad/wo/es/hin/soll

### **3. Backup in PBS überprüfen**

Liste die Backups im neuen PBS auf:

proxmox-backup-client list --repository root@pam@127.0.0.1:<Name_des_Datastores>

Falls die Backups nicht erkannt werden, kannst du den Index aktualisieren:

proxmox-backup-manager datastore verify <Name_des_Datastores>

### **4. Backup in Proxmox VE einbinden**

Falls dein PBS bereits mit einem **Proxmox VE** Server verbunden war, sollte er das Backup erkennen. Falls nicht:

1. In **Proxmox VE (PVE)** unter **Datacenter → Storage** den neuen PBS hinzufügen.
2. Das Backup über **Datacenter → Storage → PBS → Content** einsehen.

### **5. Backup wiederherstellen**

In Proxmox VE kannst du das Backup nun für einen **Container oder eine VM** wiederherstellen:

1. **PVE Web-GUI → Storage (PBS) → Content**
2. Backup auswählen und **Restore** klicken.

Alternativ über die CLI:

pct restore <Container-ID> pbs:<Datastore>:backup/ct/ID

Oder für eine VM:

qmrestore pbs:<Datastore>:backup/vm/ID <Neue-VM-ID>

---

### **Zusammenfassung**

- Backup auf neuen PBS kopieren.
- Neuen **Datastore** in PBS anlegen.
- Backup-Daten prüfen.
- PBS mit Proxmox VE verbinden.
- Backup über PVE oder CLI wiederherstellen.

Das Backup sollte jetzt wie gewohnt in Proxmox nutzbar sein.


### Wie kann ich backups exportieren?

### **Backups aus dem Proxmox Backup Server (PBS) exportieren**

Es gibt mehrere Methoden, um Backups aus dem **Proxmox Backup Server (PBS)** zu exportieren, je nach dem gewünschten Speicherziel.

---

## **1. Backups auf ein externes Laufwerk kopieren**

Falls du deine Backups z. B. auf eine **externe Festplatte oder ein NAS** sichern möchtest:

1. **Festplatte identifizieren und mounten**  
    Falls du eine USB-Festplatte nutzt, kannst du sie mit folgendem Befehl finden:
    
    ```bash
    lsblk
    ```
    
    Dann mounten:
    
    ```bash
    mount /dev/sdX /mnt/externe-platte
    ```
    
2. **Backup-Dateien kopieren**  
    Die Backup-Daten liegen standardmäßig unter `/var/lib/proxmox-backup/` oder einem definierten **Datastore-Verzeichnis** (z. B. `/mnt/datastore/mybackup`).
    
    ```bash
    rsync -avz /mnt/datastore/mybackup /mnt/externe-platte/
    ```
    
    oder einfach:
    
    ```bash
    cp -r /mnt/datastore/mybackup /mnt/externe-platte/
    ```
    

---

## **2. Backup in ein Archiv (tar.gz) verpacken**

Falls du das Backup in eine einzelne Datei exportieren möchtest, kannst du es in ein **tar.gz-Archiv** umwandeln:

tar -czvf /mnt/externe-platte/backup_export.tar.gz -C /mnt/datastore/mybackup .

---

## **3. Backup auf einen anderen Server übertragen**

Falls du dein Backup auf einen **anderen Server** kopieren möchtest:

### **Methode 1: SCP (Secure Copy)**

Einzelnes Backup übertragen:

scp -r /mnt/datastore/mybackup root@zielserver:/pfad/zum/speichern/

### **Methode 2: Rsync (schneller & effizienter)**

Falls du inkrementelle Backups machen möchtest, ist `rsync` besser:

rsync -avz /mnt/datastore/mybackup root@zielserver:/pfad/zum/speichern/

---

## **4. Backup auf ein Cloud-Speicherziel sichern**

Falls du das Backup in die **Cloud (z. B. S3, Google Drive, OneDrive)** speichern willst, kannst du `rclone` nutzen:

1. **Rclone installieren**
    
    ```bash
    apt update && apt install rclone -y
    ```
    
2. **Cloud-Anbieter einrichten**
    
    ```bash
    rclone config
    ```
    
    Hier richtest du dein Cloud-Ziel ein (z. B. Google Drive oder S3).
    
3. **Backup hochladen**
    
    ```bash
    rclone copy /mnt/datastore/mybackup remote:mein-cloud-backup
    ```
    

---

## **5. Export eines spezifischen Containers oder einer VM**

Falls du nur ein bestimmtes **Backup eines Containers oder einer VM** exportieren willst:

### **Backup-IDs auflisten**

proxmox-backup-client list --repository root@pam@127.0.0.1:<Datastore-Name>

### **Einzelnes Backup exportieren**

Backup in ein Archiv packen:

proxmox-backup-client restore vm/100/2024-02-17T12:00:00Z --repository root@pam@127.0.0.1:<Datastore-Name> --output-file /mnt/externe-platte/vm100.tar.gz

---

## **Zusammenfassung**

🔹 **Extern sichern:** `/mnt/externe-platte/` mit `rsync` oder `cp`  
🔹 **In ein Archiv packen:** `tar -czvf backup.tar.gz`  
🔹 **Auf anderen Server kopieren:** `scp` oder `rsync`  
🔹 **Cloud-Speicherung:** `rclone` für Google Drive, S3 etc.  
🔹 **Einzelnes Backup exportieren:** `proxmox-backup-client restore`

Hast du ein spezielles Ziel für deinen Export? 😊
