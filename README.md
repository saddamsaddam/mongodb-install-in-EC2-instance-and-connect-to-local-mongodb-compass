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
   docker run -d --name mongodb -e MONGO_INITDB_ROOT_USERNAME=admin -e MONGO_INITDB_ROOT_PASSWORD=ce18046 -p 27017:27017 mongo:latest

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

To connect to MongoDB running inside a Docker container on your EC2 instance using **MongoDB Compass** (a GUI client), follow these steps:

### 1. **Download MongoDB Compass (if you don't have it)**

If you don't have MongoDB Compass installed, you can download it from the official MongoDB website:

- [MongoDB Compass Download](https://www.mongodb.com/try/download/compass)

### 2. **Ensure EC2 Security Group Allows Traffic on Port 27017**

As discussed earlier, you need to ensure your EC2 instance's security group allows inbound traffic on port `27017` from your local machine.

1. **Go to the EC2 Dashboard** in the AWS Console.
2. Navigate to **Security Groups** under **Network & Security**.
3. Select the security group attached to your EC2 instance.
4. **Edit Inbound Rules** and add a rule that allows inbound traffic on port `27017`:
   - **Type**: Custom TCP Rule
   - **Port Range**: 27017
   - **Source**: Your IP address (e.g., `203.0.113.0/24` for a specific IP or `0.0.0.0/0` to allow from anywhere, though this is not recommended for production environments).

### 3. **Find Your EC2 Instance's Public IP Address**

To connect from Compass, you need the **public IP** of your EC2 instance:

- In the AWS EC2 Dashboard, click on your instance and find the **Public IP** (IPv4).

### 4. **Launch MongoDB Compass**

Open **MongoDB Compass** and follow these steps to connect:

1. **Hostname**: Enter your EC2 instance's **Public IP** (e.g., `x.x.x.x`).
   
2. **Port**: Enter `27017` (default MongoDB port).

3. **Authentication**: Select **Username/Password** for authentication.

4. **Username**: Enter `admin` (as per the environment variable you set when running the Docker container).

5. **Password**: Enter `ce18046` (your MongoDB password).

6. **Authentication Database**: Set this to `admin`.

### 5. **Connect to MongoDB**

Once you've filled in the connection details, click **Connect**. MongoDB Compass will attempt to connect to the MongoDB instance running in the Docker container on your EC2 instance.

### Example of MongoDB Compass Connection Screen:

- **Hostname**: `your-ec2-ip`
- **Port**: `27017`
- **Authentication**: 
  - **Username**: `admin`
  - **Password**: `ce18046`
  - **Authentication Database**: `admin`

### 6. **Verify the Connection**

After clicking **Connect**, MongoDB Compass should successfully connect to your MongoDB instance running in the Docker container on your EC2 instance. You can now explore your databases and collections through the GUI.


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
