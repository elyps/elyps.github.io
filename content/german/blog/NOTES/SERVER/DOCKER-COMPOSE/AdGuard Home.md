To deploy **AdGuard Home** using **Docker Compose**, follow these steps:

---

### **1. Create a Docker Compose File**

Create a directory for AdGuard Home and navigate into it:

mkdir -p ~/adguard-home && cd ~/adguard-home

Create a `docker-compose.yml` file:

version: '3.8'

services:
  adguardhome:
    container_name: adguardhome
    image: adguard/adguardhome:latest
    restart: unless-stopped
    network_mode: "host"  # Allows AdGuard Home to listen on DNS ports directly
    volumes:
      - ./config:/opt/adguardhome/conf
      - ./work:/opt/adguardhome/work
    environment:
      - TZ=Europe/Berlin # Change to your timezone

---

### **2. Start the Container**

Run the following command to start AdGuard Home:

docker compose up -d

---

### **3. Access AdGuard Home**

Once running, access the **AdGuard Home web UI** at:

- **URL**: `http://localhost:3000`
- If running on a server, replace `localhost` with the server's IP.

---

### **4. Configure AdGuard Home**

- Follow the web UI setup wizard.
- Set **DNS Server** to `0.0.0.0:53` (if using `network_mode: "host"`).
- Configure **Upstream DNS Servers** (e.g., Cloudflare `1.1.1.1`, Google `8.8.8.8`).
- Adjust filters, logging, and other settings as needed.

---

### **5. Managing AdGuard Home**

- **Stop**: `docker compose down`
- **Restart**: `docker compose restart`
- **View Logs**: `docker compose logs -f`

---

### **Optional: Running with a Custom Network**

If you don’t want to use `network_mode: "host"`, define a custom network:

networks:
  adguard_network:
    driver: bridge

And modify the `services` section:

services:
  adguardhome:
    container_name: adguardhome
    image: adguard/adguardhome:latest
    restart: unless-stopped
    ports:
      - "3000:3000"   # Web UI
      - "53:53/tcp"   # DNS
      - "53:53/udp"   # DNS
    volumes:
      - ./config:/opt/adguardhome/conf
      - ./work:/opt/adguardhome/work
    environment:
      - TZ=Europe/Berlin
    networks:
      - adguard_network

**Note:** If you use a bridge network, clients must manually set AdGuard Home’s IP as their DNS server.

---

### **6. (Optional) Automatic Updates**

If you want **AdGuard Home** to auto-update, use `watchtower`:

docker run -d \
  --name watchtower \
  -v /var/run/docker.sock:/var/run/docker.sock \
  containrrr/watchtower --cleanup

---

This setup should give you a working **AdGuard Home** deployment with **Docker Compose**. 🚀
