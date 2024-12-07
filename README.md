# Pi-Hole Docker Setup and Usage
## Introduction
Pi-hole is an open-source network-wide ad blocker that acts as a DNS sinkhole, blocking ads, trackers, and other unwanted content across your entire network. It works by intercepting DNS requests and preventing devices from resolving domain names that are known to serve ads or malicious content. With Pi-hole, you can enjoy an ad-free browsing experience on all devices connected to your network.

This project demonstrates how to deploy Pi-hole as a containerized service using Docker, manage the DNS settings, and access its web interface for monitoring and configuration.

## Learning Objectives
Docker: Learn how to run Pi-hole as a Docker container.  
Pi-hole: Understand how to use Pi-hole as a DNS ad-blocker for your network.  
Network-wide Ad Blocking: Block ads, trackers, and malware across all devices connected to your network.  

## Prerequisites
Docker: Make sure Docker is installed on your system. [Docker Installation Guide](https://docs.docker.com/get-docker/)  
Optional: Docker Compose: Use Docker Compose for easier management of the Pi-hole container. [Docker Compose Installation Guide](https://docs.docker.com/compose/install/)  
Optional: DNS Configuration: Learn how to configure your network devices to use Pi-hole for DNS resolution.  

## Steps to Set Up Pi-hole with Docker  
### 1. Pull the Pi-hole Docker Image  
First, pull the latest Pi-hole Docker image from Docker Hub:

>docker pull pihole/pihole:latest   
### 2. Run Pi-hole Container  
Start a Pi-hole container using the following command:  
>docker run -d --name pihole -p 53:53 -p 53:53/udp -p 80:80 -p 443:443 \  
  -e PIHOLE_PASSWORD=yourpassword \  
  -e WEBPASSWORD=admin \  
  -e DNS1=1.1.1.1 \  
  -e DNS2=1.0.0.1 \  
  --dns=127.0.0.1 --dns=1.1.1.1 \  
  --restart=unless-stopped \  
  pihole/pihole:latest

Alternatively, you can also use Docker Compose with the following docker-compose.yml configuration:

>version: '3.8'

>services:  
  pihole:  
    image: pihole/pihole:latest  
    container_name: pihole  
    ports:  
      - "53:53"         # DNS port (TCP/UDP)  
      - "80:80"         # Pi-hole Web interface  
      - "443:443"       # Secure access to web interface  
    environment:  
      - TZ="America/New_York"  # Set the timezone to your region  
      - WEBPASSWORD=admin       # Set a password for the Pi-hole Web interface  
      - DNS1=1.1.1.1           # Set upstream DNS (Cloudflare)  
      - DNS2=1.0.0.1           # Set secondary upstream DNS  
    volumes:  
      - pihole_data:/etc/pihole  
      - dnsmasq_data:/etc/dnsmasq.d  
    restart: unless-stopped  
volumes:  
  pihole_data:  
    driver: local  
  dnsmasq_data:  
    driver: local`

## Explanation:
-d: Run the container in detached mode.  
-e PIHOLE_PASSWORD: Set the admin password for Pi-hole (can be different from the Web interface password).  
-e WEBPASSWORD: Set the password to access Pi-hole’s web interface.  
-e DNS1 and -e DNS2: Set the upstream DNS servers (Cloudflare in this case).  
--dns=127.0.0.1: Ensures Pi-hole uses the local DNS server.  
--restart=unless-stopped: Restart the container unless stopped manually.  

### 3. Verify Running Containers
To check if Pi-hole is running, use the following command:  
>docker ps

This will display all active containers. Look for pihole in the list.

### 4. Access the Pi-hole Web Console  
Pi-hole provides a web interface to manage your DNS settings and view stats. Open your web browser and navigate to:

>http://localhost/admin

Login with the following credentials:  
Username: admin 
Password: The password you set with the WEBPASSWORD environment variable.  

### 5. Configure Pi-hole for Your Network
Once logged in, you can configure Pi-hole as your DNS server for all devices in your network.

Change DNS settings: Set Pi-hole as the DNS server on your router or individual devices to route DNS requests through Pi-hole.  
Block or whitelist domains: Manage blocked domains from the Pi-hole dashboard. You can whitelist domains you want to allow or blacklist domains to block.  
View statistics: Pi-hole shows how many queries have been blocked and allowed, and you can analyze queries in the Query Log.  

### 6. Monitor and Manage Pi-hole  
You can access Pi-hole’s dashboard to:  
View network activity  
See blocked requests  
Configure settings like upstream DNS servers  
Access additional tools like the Query Log and Blocklists  

### 7. Troubleshoot and Update Pi-hole  
To troubleshoot, check the Pi-hole logs:  
>docker exec -it pihole tail -f /var/log/pihole.log

To update Pi-hole, you can pull the latest Docker image and restart the container:
>docker pull pihole/pihole:latest
docker restart pihole

### 8. Stop and Remove the Pi-hole Container
To stop the container:
>docker stop pihole

To remove the container:
>docker rm pihole

## Conclusion  
This project demonstrates how to quickly set up and use Pi-hole as a containerized DNS ad-blocking service using Docker. With Pi-hole, you can block ads, trackers, and malicious content across all devices on your network, enhancing privacy and performance.

## Future Improvements  
1. Backup & Restore:Learn how to backup and restore Pi-hole data and settings.  
2. Advanced Configuration:Explore advanced Pi-hole features such as conditional forwarding, local DNS records, and DNS-over-HTTPS.  
3. Docker Compose:Use Docker Compose to automate and simplify multi-container setups (e.g., combining Pi-hole with other services).  
4. Integration:Learn how to integrate Pi-hole with other tools, such as network monitoring systems.  

## References
[Pi-Hole on Docker Hub](https://hub.docker.com/r/pihole/pihole)
