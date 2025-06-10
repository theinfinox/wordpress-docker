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

---

## Hosting Multiple WordPress Sites

Repeat the steps above for each site, using unique networks, volumes, container names, and ports:  
Below is an example for **three websites**:

```bash
# Create networks
docker network create wordpress-network1
docker network create wordpress-network2
docker network create wordpress-network3

# Create volumes
docker volume create --name wordpress_data1
docker volume create --name wordpress_data2
docker volume create --name wordpress_data3
docker volume create --name mariadb_data1
docker volume create --name mariadb_data2
docker volume create --name mariadb_data3

# MariaDB containers
docker run -d --name mariadb1 ...
docker run -d --name mariadb2 ...
docker run -d --name mariadb3 ...

# WordPress containers
docker run -d --name wordpress1 ...
docker run -d --name wordpress2 ...
docker run -d --name wordpress3 ...
```
Adjust ports (`8081`, `8082`, etc.) and database names for each instance.

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

MIT

---

## References

- [Bitnami WordPress Docker Image](https://hub.docker.com/r/bitnami/wordpress)
- [Bitnami MariaDB Docker Image](https://hub.docker.com/r/bitnami/mariadb)
- [Docker Volumes](https://docs.docker.com/storage/volumes/)
- [Watchtower](https://containrrr.dev/watchtower/)
