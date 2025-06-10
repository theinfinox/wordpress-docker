# Run Multiple WordPress Sites with Docker using Bitnami Stack

Easily host and manage one or more WordPress websites using Docker containers powered by the Bitnami WordPress and MariaDB images.  
This repository provides clear instructions for running isolated WordPress sites—each with its own database—on your server.  
**Ideal for development, staging, or small-scale production use.**

---

## Key Features

- **Multiple, isolated WordPress sites:** Each with its own database, files, and network.
- **Reliable & updatable:** Use [Watchtower](https://containrrr.dev/watchtower/) for automated container updates, or update WordPress directly from its dashboard.
- **Customizable storage:** Map Docker volumes to host directories for easy plugin/theme management.
- **Automatic restart:** Containers restart automatically on failure to minimize downtime.
- **Open to contributions:** Fork and improve this template to help the community!

---

## Quick Start Example: One WordPress Site

**1. Create a Docker network**  
```bash
docker network create wordpress-network1
```

**2. Create persistent volumes**  
```bash
docker volume create --name wordpress_data1
docker volume create --name mariadb_data1
```

**3. Start the MariaDB container**  
```bash
docker run -d --name mariadb1 \
  --env ALLOW_EMPTY_PASSWORD=yes \
  --env MARIADB_USER=bn_wordpress \
  --env MARIADB_PASSWORD=bitnami \
  --env MARIADB_DATABASE=bitnami_wordpress1 \
  --network wordpress-network1 \
  --restart unless-stopped \
  --volume mariadb_data1:/bitnami/mariadb \
  bitnami/mariadb:latest
```

**4. Start the WordPress container**  
```bash
docker run -d --name wordpress1 \
  -p 8081:8080 -p 8443:8443 \
  --env ALLOW_EMPTY_PASSWORD=yes \
  --env WORDPRESS_DATABASE_HOST=mariadb1 \
  --env WORDPRESS_DATABASE_PORT_NUMBER=3306 \
  --env WORDPRESS_DATABASE_USER=bn_wordpress \
  --env WORDPRESS_DATABASE_PASSWORD=bitnami \
  --env WORDPRESS_DATABASE_NAME=bitnami_wordpress1 \
  --network wordpress-network1 \
  --restart unless-stopped \
  --volume wordpress_data1:/bitnami/wordpress \
  bitnami/wordpress:latest
```

- **Default login:**  
  Username: `user`  
  Password: `bitnami`
  
NB: Default password are meant to be changed on first login itself.  User can also be changed. Just remember the needful. 

![image](https://github.com/user-attachments/assets/7ebad477-88f2-40f2-aeab-099cf5fd42ee)


---

## Exposing Your WordPress Site to the Internet

To make your WordPress site accessible from outside your local network, follow these steps and tips:

### 1. Check and Configure Your Firewall

- **Linux Servers:**  
  If you are using `ufw` (Uncomplicated Firewall), check the status and allow the required ports:
  ```bash
  sudo ufw status
  sudo ufw allow 8081/tcp   # Example for WordPress site 1
  sudo ufw allow 8443/tcp   # Example for HTTPS port
  ```

> ⚠️ **Warning:**
> Leave ufw as it is if inactive, if activating Do understnd that:
> Before editing your firewall settings (such as `ufw`), be extremely careful!  
> If you accidentally block or remove rules for SSH (`port 22`), you may lose remote access to your server.  
> Always ensure you have a rule allowing SSH (e.g., `sudo ufw allow 22/tcp`) before making other changes.  
> If possible, have console or physical access to your server as a backup.

Later
  Replace the port numbers as needed for your setup.

- **Other Firewall Tools:**  
  Use your system's firewall management tool to allow incoming connections on the relevant ports.

### 2. Map Ports to Your Domain

- Once your server is accessible on the desired ports, you can point your domain to your server's **static IP** using DNS A records.
- In your domain registrar's DNS settings, set an A record to your server's public IP.

### 3. Dynamic DNS (DDNS) and Cloudflare Zero Trust

- **If your server does not have a static IP:**  
  Use a Dynamic DNS (DDNS) service to automatically update your domain with your changing public IP.
- **Cloudflare Zero Trust:**  
  Use Cloudflare's Zero Trust platform to securely expose your site even without a static IP.

### 4. Bonus Tips for Student and Budding Developers

- **DDNS for Home Servers:**  
  DDNS services allow you to run a web server from home using a Raspberry Pi, an old laptop, or even your main computer. Your site remains accessible even if your IP address changes.
- **Try ngrok:**  
  [ngrok](https://ngrok.com/) lets you expose your local development server to the internet with a public URL—great for testing, demos, or temporary sharing.

---

> **Summary:**  
> - Open required ports in your firewall (e.g., 8081 for WordPress HTTP).
> - Point your domain to your server using DNS.
> - Use DDNS or tools like Cloudflare Zero Trust if you do not have a static IP.
> - Try ngrok for quick, secure public exposure of local sites.


---

## Hosting Multiple WordPress Sites

Repeat the steps above for each site, using unique networks, volumes, container names, and ports:  
Below is an example for **three websites**:


To set up multiple WordPress instances with separate databases using Docker, you'll need to modify the Docker commands to create additional containers for WordPress and MariaDB. Here's how you can set it up for three websites:

1. Create Docker networks:
```bash
docker network create wordpress-network1
docker network create wordpress-network2
docker network create wordpress-network3
```

2. Create volumes for each WordPress instance and database:
```bash
docker volume create --name wordpress_data1
docker volume create --name wordpress_data2
docker volume create --name wordpress_data3

docker volume create --name mariadb_data1
docker volume create --name mariadb_data2
docker volume create --name mariadb_data3
```

3. Run MariaDB containers for each WordPress instance:
```bash
docker run -d --name mariadb1 \
  --env ALLOW_EMPTY_PASSWORD=yes \
  --env MARIADB_USER=bn_wordpress \
  --env MARIADB_PASSWORD=bitnami \
  --env MARIADB_DATABASE=bitnami_wordpress1 \
  --network wordpress-network1 \
  --volume mariadb_data1:/bitnami/mariadb \
  bitnami/mariadb:latest

docker run -d --name mariadb2 \
  --env ALLOW_EMPTY_PASSWORD=yes \
  --env MARIADB_USER=bn_wordpress \
  --env MARIADB_PASSWORD=bitnami \
  --env MARIADB_DATABASE=bitnami_wordpress2 \
  --network wordpress-network2 \
  --volume mariadb_data2:/bitnami/mariadb \
  bitnami/mariadb:latest

docker run -d --name mariadb3 \
  --env ALLOW_EMPTY_PASSWORD=yes \
  --env MARIADB_USER=bn_wordpress \
  --env MARIADB_PASSWORD=bitnami \
  --env MARIADB_DATABASE=bitnami_wordpress3 \
  --network wordpress-network3 \
  --volume mariadb_data3:/bitnami/mariadb \
  bitnami/mariadb:latest
```

4. Run WordPress containers for each WordPress instance:
```bash
docker run -d --name wordpress1 \
  -p 8081:8080 -p 8443:8443 \
  --env ALLOW_EMPTY_PASSWORD=yes \
  --env WORDPRESS_DATABASE_HOST=mariadb1 \
  --env WORDPRESS_DATABASE_PORT_NUMBER=3306 \
  --env WORDPRESS_DATABASE_USER=bn_wordpress \
  --env WORDPRESS_DATABASE_PASSWORD=bitnami \
  --env WORDPRESS_DATABASE_NAME=bitnami_wordpress1 \
  --network wordpress-network1 \
  --volume wordpress_data1:/bitnami/wordpress \
  bitnami/wordpress:latest

docker run -d --name wordpress2 \
  -p 8082:8080 -p 8444:8443 \
  --env ALLOW_EMPTY_PASSWORD=yes \
  --env WORDPRESS_DATABASE_HOST=mariadb2 \
  --env WORDPRESS_DATABASE_PORT_NUMBER=3306 \
  --env WORDPRESS_DATABASE_USER=bn_wordpress \
  --env WORDPRESS_DATABASE_PASSWORD=bitnami \
  --env WORDPRESS_DATABASE_NAME=bitnami_wordpress2 \
  --network wordpress-network2 \
  --volume wordpress_data2:/bitnami/wordpress \
  bitnami/wordpress:latest

docker run -d --name wordpress3 \
  -p 8083:8080 -p 8445:8443 \
  --env ALLOW_EMPTY_PASSWORD=yes \
  --env WORDPRESS_DATABASE_HOST=mariadb3 \
  --env WORDPRESS_DATABASE_PORT_NUMBER=3306 \
  --env WORDPRESS_DATABASE_USER=bn_wordpress \
  --env WORDPRESS_DATABASE_PASSWORD=bitnami \
  --env WORDPRESS_DATABASE_NAME=bitnami_wordpress3 \
  --network wordpress-network3 \
  --volume wordpress_data3:/bitnami/wordpress \
  bitnami/wordpress:latest
```

This will set up three separate WordPress instances with their own databases using Docker containers. Adjust port numbers and environment variables as needed for your setup.



Adjust ports (`8081`, `8082`, etc.) and database names for each instance.

---

## Accessing Your WordPress and Database Containers

Once your containers are running, you can access and interact with your WordPress sites and MariaDB databases as follows:

### Accessing WordPress Sites

Each WordPress container is mapped to a unique port on your host machine.  
Open your web browser and go to the following addresses:

- **Site 1:** [http://localhost:8081](http://localhost:8081)
- **Site 2:** [http://localhost:8082](http://localhost:8082) *(if enabled)*
- **Site 3:** [http://localhost:8083](http://localhost:8083) *(if enabled)*

> **Note:** If you are running Docker on a remote server, replace `localhost` with your server's IP address.

### Accessing the WordPress Admin Dashboard

For each site:
- Go to `http://localhost:PORT/wp-admin`  
  (e.g., [http://localhost:8081/wp-admin](http://localhost:8081/wp-admin))
- Default credentials:  
  **Username:** `user`  
  **Password:** `bitnami`

> **Tip:** Change your password after logging in for the first time, especially in production environments.

### Accessing MariaDB Databases

You can connect to the MariaDB containers directly using tools like [MySQL Workbench](https://www.mysql.com/products/workbench/), [DBeaver](https://dbeaver.io/), or the MySQL CLI.

#### Example: Access MariaDB via the CLI

To open a shell in the MariaDB container (e.g., for Site 1):
```bash
docker exec -it mariadb1 mysql -u bn_wordpress -pbitnami bitnami_wordpress1
```
- **Username:** `bn_wordpress`
- **Password:** `bitnami`
- **Database:** `bitnami_wordpress1` *(or `bitnami_wordpress2`, etc.)*

> **Note:** For external access (from outside the Docker host), you would need to expose the database port (3306) in your `docker run` or `docker-compose.yml` file. By default, the database is only accessible by the WordPress container in its Docker network for security.

### Accessing Container Shells

You can get a shell inside any container for troubleshooting or advanced management:

```bash
docker exec -it wordpress1 bash     # For WordPress container shell
docker exec -it mariadb1 bash       # For MariaDB container shell
```

---

**Summary Table**

| Site        | WordPress URL      | Admin URL                   | DB Container | DB Name               | DB User       |
|-------------|-------------------|-----------------------------|--------------|-----------------------|---------------|
| Site 1      | http://localhost:8081 | http://localhost:8081/wp-admin | mariadb1     | bitnami_wordpress1    | bn_wordpress  |
| Site 2*     | http://localhost:8082 | http://localhost:8082/wp-admin | mariadb2     | bitnami_wordpress2    | bn_wordpress  |
| Site 3*     | http://localhost:8083 | http://localhost:8083/wp-admin | mariadb3     | bitnami_wordpress3    | bn_wordpress  |

\*If enabled in your configuration.




---

## Optional: Map Volumes to Local Folders

To manage plugins/themes from your host machine, map volumes to specific directories:
```bash
docker run -d --name wordpress1 \
  ...
  --volume /path/on/host/wordpress1:/bitnami/wordpress \
  bitnami/wordpress:latest
```
Replace `/path/on/host/wordpress1` with your desired directory.

---

## Updates & Maintenance

- **Automatic updates:**  
  Use [Watchtower](https://containrrr.dev/watchtower/) to auto-update WordPress and MariaDB containers.
- **Manual WordPress updates:**  
  Update directly from the WordPress dashboard if desired.

---

## Best Practices

- **Restart policies:**  
  The `--restart unless-stopped` flag ensures your containers restart automatically after failures or reboots.
- **Security:**  
  Set strong passwords in production. The above uses defaults for simplicity.
- **Backups:**  
  Regularly back up your volumes for both WordPress and MariaDB.

---

## Contributing

This is a basic template—**fork and update it** to make it better for everyone!  
Pull requests, issues, and suggestions are welcome.

---

## License

  GNU GENERAL PUBLIC LICENSE
  Version 3, 29 June 2007

---

## References

- [Bitnami WordPress Docker Image](https://hub.docker.com/r/bitnami/wordpress)
- [Bitnami MariaDB Docker Image](https://hub.docker.com/r/bitnami/mariadb)
- [Docker Volumes](https://docs.docker.com/storage/volumes/)
- [Watchtower](https://containrrr.dev/watchtower/)
