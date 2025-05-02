Hier ist ein PowerShell-Skript, das den Ordner `/mnt/datastore/mybackup` auf neue Dateien überwacht und automatisch den Befehl `rclone copy` ausführt, sobald eine neue Datei hinzugefügt wird.

Das Skript verwendet einen `FileSystemWatcher`, um den Ordner zu überwachen. Sobald eine neue Datei erkannt wird, wird der `rclone copy`-Befehl ausgeführt, um die Datei in dein Cloud-Backup hochzuladen. 

Das Skript wurde aktualisiert, sodass es nun im Hintergrund läuft. Der Überwachungsprozess bleibt aktiv, ohne dass das Hauptfenster blockiert wird.

#!/bin/bash

WATCH_DIR="/mnt/datastore/mybackup"
REMOTE_DIR="remote:mein-cloud-backup"

inotifywait -m -e create --format "%w%f" "$WATCH_DIR" | while read NEW_FILE
do
    echo "Neue Datei erkannt: $NEW_FILE. Backup wird gestartet..."
    rclone copy "$WATCH_DIR" "$REMOTE_DIR" --ignore-existing &
done &

echo "Überwachung gestartet. Das Skript läuft im Hintergrund."


$watcher = New-Object 
System.IO.FileSystemWatcher 
$watcher.Path = "/mnt/datastore/mybackup" 
$watcher.Filter = "*.*" 
$watcher.IncludeSubdirectories = $false 
$watcher.EnableRaisingEvents = $true 
$action = { Start-Process -NoNewWindow -FilePath "rclone" -ArgumentList "copy /mnt/datastore/mybackup remote:mein-cloud-backup" Write-Host "Neue Datei erkannt. Backup gestartet..." } Register-ObjectEvent -InputObject 
$watcher -EventName "Created" -Action 
$action Write-Host "Überwachung gestartet. Das Skript läuft im Hintergrund." 
$job = Start-Job -ScriptBlock { while ($true) { Start-Sleep -Seconds 10 } } Wait-Job -Id 
$job.Id
