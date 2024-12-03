**Overview**
This project provides a Docker Swarm deployment for DOMjudge, including three services:

1. **MariaDB** : Database backend for DOMjudge.

2. **DOMserver** : Web interface and central server for DOMjudge.

3. **Judgehost** : Dynamically deployed judge nodes for contest evaluation.
   **Prerequisites**

- Docker and Docker Compose installed on all nodes.

- A configured Docker Swarm cluster with the following node roles:

  - **`domserver`** : Manager node.

  - **`mariadb`** : Worker node.

  - **`judgehost`** : Worker node.

---

**Usage** **1. Configuration**

1. Clone this repository:

```bash
git clone <repository-url>
cd <repository-folder>
```

2. Create a `.env` file and configure the following variables:

```env
DB_PASS=domjudgepass
DOMSERVER_BASEURL=http://domserver/
JUDGE_PASS=domjudge
```

3. Verify the `docker-compose.yml` file aligns with your requirements (e.g., network configuration, resource constraints).
   **2. Initialize the Swarm** On the `domserver`\*\* node:

```bash
docker swarm init --advertise-addr <MANAGER-IP>
```

**3. Add nodes**

On the `mariadb`** and the `judgehost`** node:

```bash
docker swarm join --token <TOKEN> <MANAGER-IP>:2377

```

**4. Label Nodes**
Label the nodes to define roles:

```bash
docker node update --label-add role=domserver <DOMSERVER-NODE-NAME>
docker node update --label-add role=mariadb <MARIADB-NODE-NAME>
docker node update --label-add role=judgehost <JUDGEHOST-NODE-NAME>
```

**5. Deploy the Stack**
Deploy the DOMjudge stack:

```bash
docker stack deploy -c docker-compose.yml domjudge
```

Monitor the logs to ensure services start successfully:

```bash
docker service logs -f domjudge_domserver
```

**6. Retrieve Initial Passwords**
Wait for the DOMserver to initialize and look for the following logs:

```text
domserver_1  | Initial admin password is XXXXXXXXXX
domserver_1  |
domserver_1  | Initial judgehost password is XXXXXXXXXXXXX
```

- Log in to the DOMjudge web interface at `http://<DOMSERVER-IP>:12345` with:

  - **Username:** `admin`

  - **Password:** `<Admin Password>`

- Change the admin password immediately.

- Update the `.env` file with the retrieved judgehost password:

```env
JUDGE_PASS=<Judgehost Password>
```

**7. Restart the Stack** Apply the updated `.env` file:

```bash
docker stack deploy -c docker-compose.yml domjudge
```

---

**Advanced Usage** **Scaling Judgehosts**
To scale the number of judgehosts:

```bash
docker service scale domjudge_judgehost=<NUMBER-OF-JUDGEHOSTS>
```

**Restricting Judgehost Placement**
To ensure judgehosts run only on worker nodes:

1. Set the `RESTRICT_JUDGE_ON` variable in `.env` to `manager`.

---

**Troubleshooting** **Service Fails to Start**

1. Check logs for errors:

```bash
docker service logs <SERVICE-NAME>
```

2. Verify node roles and labels:

```bash
docker node inspect <NODE-NAME>
```

**Connectivity Issues** Ensure overlay network (`dj_network`) is functioning:

```bash
docker exec -it <CONTAINER-ID> ping domserver
```
