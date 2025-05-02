​Nginx Proxy Manager (NPM) is a user-friendly interface for managing Nginx as a reverse proxy, simplifying tasks like forwarding web services, obtaining SSL certificates via Let's Encrypt, and configuring multiple users. 

**Key Features:**

- **User-Friendly Interface:** NPM offers an intuitive web-based dashboard for easy management of proxy hosts.
- **Automated SSL Management:** It integrates with Let's Encrypt to provide free SSL certificates, which are automatically renewed.
- **Multi-User Support:** You can create multiple users with specific access permissions, enhancing security and collaboration.
- **Advanced Configuration:** For experienced users, NPM allows custom Nginx configurations to meet specific needs.

**Installation Steps:**

1. **Prepare the Environment:**
    - Ensure Docker and Docker Compose are installed on your system.
    - Create a directory for NPM and navigate into it:
        
        ```bash
        mkdir nginx-proxy-manager
        cd nginx-proxy-manager
        ```
        
2. **Create Docker Compose File:**
    - Create a `docker-compose.yml` file with the following content:
        
        ```yaml
        version: '3.8'
        services:
          app:
            image: 'jc21/nginx-proxy-manager:latest'
            restart: unless-stopped
            ports:
              - '80:80'    # Public HTTP Port
              - '443:443'  # Public HTTPS Port
              - '81:81'    # Admin Web Port
            environment:
              DB_SQLITE_FILE: "/data/database.sqlite"
            volumes:
              - ./data:/data
              - ./letsencrypt:/etc/letsencrypt
        ```
        
3. **Start Nginx Proxy Manager:**
    - Launch the application using Docker Compose:
        
        ```bash
        docker-compose up -d
        ```
        
4. **Access the Admin Interface:**
    - Once the container is running, access the admin interface at `http://<your-ip>:81`.
    - Log in with the default credentials:
        - **Email:** `admin@example.com`
        - **Password:** `changeme`
    - Immediately after logging in, you'll be prompted to change these credentials for security purposes.

For detailed setup instructions and additional configuration options, refer to the official Nginx Proxy Manager documentation. 
