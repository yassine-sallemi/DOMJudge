# DOMjudge Docker Setup

This repository contains a Docker Compose setup for running a DOMjudge environment, including:

- **MariaDB**: Database server for storing DOMjudge data.
- **DOMserver**: The main server for managing contests and participants.
- **Judgehost**: Worker nodes that evaluate submitted solutions.

## Prerequisites

- **Docker and Docker Compose** installed on your system.
- **Control Groups (cgroups)** enabled on your host system to allow the judgehost to manage resource constraints.
- **Adequate permissions** to run Docker containers.

## Getting Started

### 1. Clone this Repository

```bash
git clone <repository-url>
cd <repository-directory>
```

### 2. Update Configuration

Replace the placeholder `TO_BE_REPLACED_AFTER_DOMSERVER_INIT` in the `judgehost-0` section of the `docker-compose.yml` file with the actual password after initializing the DOMserver.
You can retrieve it from the `DOMServer` container from the logs
Alternatively, the password can be found in the file `/opt/domjudge/domserver/etc/restapi.secret` within the `DOMServer` container.

### 3. Start the Containers

Run the following command to start all services:

```bash
docker compose up -d
```

### 4. Access the Services

- **DOMserver** : Open your browser and navigate to `http://localhost:12345`.

- **MariaDB** : Connect via `localhost:13306`.

### 5. Stopping the Containers

To stop the services, run:

```bash
docker compose down
```

## Customization

- **Database Configuration** : Update the MariaDB settings (e.g., user, password, and database) in the `environment` section of the `mariadb` service.

- **Judgehost Scaling** : Add additional judgehost containers by copying the `judgehost-0` section and incrementing the `DAEMON_ID` value.

## Notes

- Ensure the `DJ_DB_INSTALL_BARE` is set to `1` for the first run to initialize the database.

- The `volumes` configuration for MariaDB (`/var/lib/mysql`) should point to a persistent directory on the host for data storage.

## Troubleshooting

- Check logs for a specific service:

```bash
docker logs <container_name>
```

- Verify container status:

```bash
docker ps
```

## References

- [DOMjudge Documentation](https://www.domjudge.org/docs/manual/8.2/index.html)

---

Feel free to contribute or report issues!
