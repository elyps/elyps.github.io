Derzeit gibt es kein offizielles RetroPie-Image für den Raspberry Pi 5. Dennoch ist es möglich, RetroPie manuell auf einem Raspberry Pi 5 zu installieren, indem man es über das Raspberry Pi OS (Debian 12 "Bookworm") installiert. Hier ist eine Schritt-für-Schritt-Anleitung:

## Installation

1. **Raspberry Pi OS Lite (64-Bit) installieren:**
    
    - Laden Sie das Raspberry Pi OS Lite (64-Bit) herunter und schreiben Sie es mit dem Raspberry Pi Imager auf eine microSD-Karte.
     
     RasberryPi Os Images:
    - [Operating system images – Raspberry Pi](https://www.raspberrypi.com/software/operating-systems/)
    
	  RaspberryPi Imager
    - [Raspberry Pi OS – Raspberry Pi](https://www.raspberrypi.com/software/)
    
2. **Systemkonfiguration:**
    
    - Starten Sie den Raspberry Pi und führen Sie grundlegende Konfigurationen durch, wie die Einstellung von Sprache, Zeitzone und Tastaturlayout.
3. **RetroPie manuell installieren:**
    
    - Aktualisieren Sie das System:
        
        ```
        sudo apt update
        sudo apt upgrade
        ```
        
    - Klonen Sie das RetroPie-Setup-Skript:
        
        ```
        git clone --branch master https://github.com/RetroPie/RetroPie-Setup.git
        ```
        
    - Wechseln Sie in das Setup-Verzeichnis:
        
        ```
        cd RetroPie-Setup
        ```
        
    - Starten Sie das Setup-Skript:
        
        ```
        sudo ./retropie_setup.sh
        ```
        
    - Wählen Sie im Menü "Basic Install" und folgen Sie den Anweisungen auf dem Bildschirm. 
    
4. **EmulationStation starten:**
    
    - Nach der Installation können Sie EmulationStation mit folgendem Befehl starten:
        
        ```
        emulationstation
        ```
        

## Besonderheiten

Bitte beachten Sie, dass einige Emulatoren, insbesondere für das **Nintendo 64**, zusätzliche Konfigurationen erfordern können. Einige Benutzer haben berichtet, dass das Ändern des CPU-Kerns auf "cached_interpreter" die Leistung verbessert. Dies kann durch Hinzufügen der folgenden Zeile zur entsprechenden Konfigurationsdatei erreicht werden:


parallel-n64-cpucore = "cached_interpreter"



Für eine visuelle Anleitung zur Installation von RetroPie auf dem Raspberry Pi 5 können Sie sich dieses Video ansehen:

<iframe width="560" height="315" src="https://www.youtube.com/embed/PAePvz6YSWo?si=byGwVopCQFJUzmby" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>


## Setup for PS2

<iframe width="560" height="315" src="https://www.youtube.com/embed/WIcxs50GP18?si=hHOXx_eJ7Cc7j34S" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Main instructions - fozid   [![](https://www.gstatic.com/youtube/img/watch/social_media/reddit_1x.png) / ps2_gaming_on_the_pi_4_with_64bit_retropie](https://www.youtube.com/redirect?event=video_description&redir_token=QUFFLUhqbWNaUUZxUFotclBsbW05OEpqX2d0bGxMb2E5Z3xBQ3Jtc0trc1VPNlQ1VmN5b01icTc5YkpxRElXYk9DQW9jaU1yemRrRkYxR2pIVlpfaURQRjR3S1U3YlNvNnFwYmZSaTJFd1Q4MGpyZVh2VVVidU5pTFh1Mk4zQlZ3TWdaX2dseC1yRFowVzN5c0lXdnF2V1dOWQ&q=https%3A%2F%2Fwww.reddit.com%2Fr%2FRetroPie%2Fcomments%2F1cqw8sv%2Fps2_gaming_on_the_pi_4_with_64bit_retropie%2F&v=WIcxs50GP18)   
GitHub Repo - Exarkuniv [https://github.com/Exarkuniv/RetroPie...](https://www.youtube.com/redirect?event=video_description&redir_token=QUFFLUhqazctbzlfZ1llREctM0ZQQ0hJUllWMHFXaGhSZ3xBQ3Jtc0tuUDNDN0h3Y1JnbmpzaTJCVzl2TklUNFNqU05qOWo2Rnc4MlBIdHZqdWw3WGoteG5SbThCeFZVOFhKWlpVeTloNXF4NExSR0lXRGdGU2xlYlRfb3pra2s1YUNBVUZXREpzcS1ZekFvQnNIaVRXcUFBSQ&q=https%3A%2F%2Fgithub.com%2FExarkuniv%2FRetroPie-Extra&v=WIcxs50GP18) 
Kernal 8 change - lifeinthefastline   [![](https://www.gstatic.com/youtube/img/watch/social_media/reddit_1x.png) / aethersx2_on_retropie_pi5_issues](https://www.youtube.com/redirect?event=video_description&redir_token=QUFFLUhqazVoaUpXcnhhOUJ3cTlrVmRyZHJwbVRtQlFsQXxBQ3Jtc0tscFYtLWZPR25QOUlROU03TUwtR2I4YTVwbzJ2ZXVDZ0k2SHJhTWxTWk5TUDlyakdKRGxyRTQxaE5jUG9pX2daLXl4YVBxR2o3aHhaYzN6NW14NGV5SjgzVHpwRktQVXRHcGQyWkJ2T2dSdkZ2U1FIdw&q=https%3A%2F%2Fwww.reddit.com%2Fr%2FRetroPie%2Fcomments%2F1e0duvq%2Faethersx2_on_retropie_pi5_issues&v=WIcxs50GP18)
