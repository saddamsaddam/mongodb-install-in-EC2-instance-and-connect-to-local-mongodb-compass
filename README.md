# mongodb-install-in-EC2-instance-and-connect-to-local-mongodb-compass

If you're using Docker on your EC2 instance and want to install the latest version of MongoDB, you can follow these steps to run MongoDB in a Docker container.

### 1. **Install Docker on your EC2 instance**

If you haven't installed Docker yet, follow these steps:

1. Update your system:
   ```bash
   sudo yum update -y
   ```

2. Install Docker:
   ```bash
   sudo amazon-linux-extras install docker -y
   ```

3. Start and enable the Docker service:
   ```bash
   sudo service docker start
   sudo systemctl enable docker
   ```

4. Add your user to the Docker group (optional, to avoid using `sudo` with Docker commands):
   ```bash
   sudo usermod -a -G docker ec2-user
   ```

   Log out and log back in for the changes to take effect.

### 2. **Pull the latest MongoDB Docker image**

Now that Docker is installed, you can pull the latest MongoDB image from Docker Hub:

```bash
docker pull mongo:latest
```

### 3. **Run MongoDB in a Docker container**

Run a MongoDB container using the `docker run` command. You can specify environment variables like the `MONGO_INITDB_ROOT_USERNAME` and `MONGO_INITDB_ROOT_PASSWORD` to create an admin user.

For example, to run MongoDB with authentication enabled:

```bash
docker run -d \
  --name mongodb \
  -e MONGO_INITDB_ROOT_USERNAME=admin \
  -e MONGO_INITDB_ROOT_PASSWORD=yourpassword \
  -p 27017:27017 \
  mongo:latest
```

- `-d` runs the container in the background.
- `--name mongodb` gives the container a name.
- `-e MONGO_INITDB_ROOT_USERNAME=admin` sets the MongoDB root username.
- `-e MONGO_INITDB_ROOT_PASSWORD=yourpassword` sets the MongoDB root password.
- `-p 27017:27017` maps the container's MongoDB port to the EC2 instance's port `27017`.

### 4. **Verify MongoDB is running**

To verify that MongoDB is running inside the container, use the following command:

```bash
docker ps
```

You should see the `mongodb` container running.

### 5. **Connect to MongoDB**

To connect to MongoDB from your EC2 instance, you can use the following command:

```bash
docker exec -it mongodb mongo -u admin -p yourpassword --authenticationDatabase admin
```

This will open the MongoDB shell where you can run commands.

### 6. **Allow external connections (optional)**

If you want to allow external connections to MongoDB, ensure that your EC2 instance’s security group allows inbound traffic on port `27017`.

Go to the **EC2 Dashboard** > **Security Groups** and modify the inbound rules to allow traffic on port `27017` (TCP) from your desired IP.

### 7. **Stopping and restarting MongoDB container**

To stop the MongoDB container, use:

```bash
docker stop mongodb
```

To restart it:

```bash
docker start mongodb
```

### 8. **Persisting MongoDB data (optional)**

By default, the MongoDB data will be lost if the container is removed. To persist the data, you can use Docker volumes.

Run MongoDB with a volume to store the database data on the host:

```bash
docker run -d \
  --name mongodb \
  -e MONGO_INITDB_ROOT_USERNAME=admin \
  -e MONGO_INITDB_ROOT_PASSWORD=yourpassword \
  -v /path/on/host:/data/db \
  -p 27017:27017 \
  mongo:latest
```

Replace `/path/on/host` with the directory on your EC2 instance where you want to store the MongoDB data.

---

That's it! You have MongoDB running in a Docker container on your EC2 instance.
