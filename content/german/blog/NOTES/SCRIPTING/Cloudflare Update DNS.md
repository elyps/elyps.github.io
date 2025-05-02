#!/bin/bash
# Konfiguration
CF_API_TOKEN="rftLb4AlvMncjafeueH1bqwm4pCjTSyciZesbG2f" # Ersetze mit deinem Cloudflare-API-Token
CF_ZONE_ID="9e6d4db11c1fe1aea24cf0808123e572"   # Ersetze mit deiner Zone-ID
DOMAIN="bastian-fischer.dev"  # Ersetze mit deiner Hauptdomain
# "@" steht hier für die Hauptdomain
RECORDS=("@" "vault" "git" "links" "cicd" "books" "dns" "docs" "drive" "hub" "lib" "sync" "time")

# Funktion zum Abrufen der aktuellen öffentlichen IP-Adresse
get_public_ip() {
    curl -s https://api.ipify.org
}

# Funktion zum Ermitteln des vollständigen Domainnamens
get_full_domain() {
    local entry=$1
    if [ "$entry" = "@" ]; then
        echo "$DOMAIN"
    else
        echo "$entry.$DOMAIN"
    fi
}

# Funktion zum Abrufen der aktuellen IP auf Cloudflare
get_cloudflare_ip() {
    local entry=$1
    local fqdn
    fqdn=$(get_full_domain "$entry")
    curl -s -X GET "https://api.cloudflare.com/client/v4/zones/$CF_ZONE_ID/dns_records?type=A&name=$fqdn" \
        -H "Authorization: Bearer $CF_API_TOKEN" \
        -H "Content-Type: application/json" | jq -r '.result[0].content'
}

# Funktion zum Aktualisieren der IP auf Cloudflare
update_cloudflare_ip() {
    local entry=$1
    local new_ip=$2
    local fqdn
    fqdn=$(get_full_domain "$entry")
    local dns_record
    dns_record=$(curl -s -X GET "https://api.cloudflare.com/client/v4/zones/$CF_ZONE_ID/dns_records?type=A&name=$fqdn" \
        -H "Authorization: Bearer $CF_API_TOKEN" \
        -H "Content-Type: application/json")
    
    local record_id
    record_id=$(echo "$dns_record" | jq -r '.result[0].id')
    
    if [ "$record_id" != "null" ]; then
        curl -s -X PUT "https://api.cloudflare.com/client/v4/zones/$CF_ZONE_ID/dns_records/$record_id" \
            -H "Authorization: Bearer $CF_API_TOKEN" \
            -H "Content-Type: application/json" \
            --data '{"type":"A","name":"'"$fqdn"'","content":"'"$new_ip"'","ttl":0,"proxied":true}'
    else
        echo "Fehler: DNS-Eintrag für $fqdn nicht gefunden."
    fi
}

# Haupt-Überwachungsschleife
while true; do
    public_ip=$(get_public_ip)
    for entry in "${RECORDS[@]}"; do
        fqdn=$(get_full_domain "$entry")
        cloudflare_ip=$(get_cloudflare_ip "$entry")
        if [ "$public_ip" != "$cloudflare_ip" ]; then
            echo "IP-Adressen stimmen nicht überein. Aktualisiere $fqdn..."
            update_cloudflare_ip "$entry" "$public_ip"
        else
            echo "IP-Adressen stimmen überein für $fqdn."
        fi
    done
    sleep 5
done

