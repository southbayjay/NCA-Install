# Prerequisite: Update Ubuntu and Install Docker with Docker Compose

Before proceeding, ensure your system is up to date and Docker (with Docker Compose) is installed. Run the following commands on Ubuntu:

**Update and upgrade your system:**
```sh
sudo apt update && sudo apt upgrade -y
```

**Install required packages:**
```sh
sudo apt install -y ca-certificates curl gnupg lsb-release
```

**Add Docker’s official GPG key:**
```sh
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

**Set up the Docker repository:**
```sh
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

**Install Docker Engine, Docker CLI, containerd, and Docker Compose plugin:**
```sh
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

**Verify Docker installation:**
```sh
docker --version
```

# MiniIO (Object Storage) Setup (Required Before NCA Toolkit)

## 1. Launch MiniIO via Docker

```sh
docker run -p 9000:9000 -p 9001:9001 --name miniio \
  -v /home/ubuntu/minio-data:/data \
  -e MINIO_ROOT_USER=admin \
  -e MINIO_ROOT_PASSWORD=change_this_password \
  quay.io/minio/minio server /data --console-address ":9001"
```

## 2. Access the MiniIO Console

- **Console URL:**  
  `http://you-server-ip-address:9001`
- **Username:**  
  `admin`
- **Password:**  
  `change_this_password`

## 3. Initial Configuration

- Log into the MiniIO console.
- Create a bucket named:  
  `nca-toolkit`
- Generate Access and Secret Keys.
- Be sure to change default root credentials or create a new user with appropriate permissions.
- Save these keys.

## 4. Install NCA Toolkit

Once MiniIO is set up and your access keys are ready, run:

```sh
docker run -d -p 8080:8080 --name nca-toolkit \
  -e API_KEY=testkey123 \
  -e S3_ENDPOINT_URL=http://server-IP-address:9000 \
  -e S3_ACCESS_KEY=your_access_key \
  -e S3_SECRET_KEY=your_secret_key \
  -e S3_BUCKET_NAME=nca-toolkit \
  -e S3_REGION=None \
  stephengpope/no-code-architects-toolkit:latest
```

> Replace `your_access_key` and `your_secret_key` with the values obtained from MiniIO.
