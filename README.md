# Private Docker Registry

A self-hosted Docker registry with a web UI.

<img width="825" alt="Screenshot 2024-10-26 at 11 39 18 AM" src="https://github.com/user-attachments/assets/ec25b911-c894-483a-84a5-2033a9bf47de">


## Prerequisites

- Docker
- Docker Compose
- Existing SSL certificates

## Directory Structure

```
docker-registry/
├── auth/
│   └── htpasswd
├── certs/ (add your certs here)
│   └── domain1/ (add your domain and its certs here)
│       ├── fullchain.pem
│       └── privkey.pem
├── data/
├── .env
├── docker-compose.yml
└── README.md
```

## Setup Instructions

1. Create required directories:
    ```bash
    mkdir -p auth certs data
    ```

2. Create `.env` file:
    ```env
    # Domain Configuration
    REGISTRY_DOMAIN=domain1.lan
    REGISTRY_PORT=5555
    REGISTRY_UI_PORT=8080

    # Protocol Configuration
    REGISTRY_UI_PROTOCOL=http
    REGISTRY_PROTOCOL=https

    # SSL Certificate Paths
    SSL_CERT_DIR=./certs
    SSL_DOMAIN_DIR=domain1.lan
    SSL_CERT_FILE=fullchain.pem
    SSL_KEY_FILE=privkey.pem

    # Auth Configuration
    REGISTRY_USER=admin
    REGISTRY_PASSWORD=yourpassword
    ```

3. Create authentication credentials:
    ```bash
    # Note: Replace 'admin' and 'yourpassword' with your desired credentials
    # Make sure these match REGISTRY_USER and REGISTRY_PASSWORD in your .env file
    docker run --entrypoint htpasswd httpd:2 -Bbn admin yourpassword > auth/htpasswd
    ```

4. Place your SSL certificates:
   - Place your SSL certificates in the directory specified by your environment variables:
    ```bash
    # Example path based on default .env values:
    # ./certs/domain1.lan/fullchain.pem
    # ./certs/domain1.lan/privkey.pem
    ```

5. Start the registry:
    ```bash
    docker-compose up -d
    ```

## Usage

Scroll down for a Practical Example using helloworld to test your new registry

### Login to Registry

```bash
docker login ${REGISTRY_DOMAIN}:${REGISTRY_PORT}
```

### Push Images
```bash
# Tag your image
docker tag your-image:tag ${REGISTRY_DOMAIN}:${REGISTRY_PORT}/your-image:tag
# Push to registry
docker push ${REGISTRY_DOMAIN}:${REGISTRY_PORT}/your-image:tag
```

### Pull Images
```bash
docker pull ${REGISTRY_DOMAIN}:${REGISTRY_PORT}/your-image:tag
```
## Web Interface
Access the registry UI at: `${REGISTRY_UI_PROTOCOL}://${REGISTRY_DOMAIN}:${REGISTRY_UI_PORT}`

## Default Credentials
```
Username: ${REGISTRY_USER} (as set in .env and htpasswd)
Password: ${REGISTRY_PASSWORD} (as set in .env and htpasswd)
```

## Maintenance

### View Logs
```bash
docker-compose logs -f
```

### Stop Registry
```bash
docker-compose down
```

## Backup

The registry data is stored in the data directory. Back up this directory along with the auth and certs directories.

## Practical Example Using Hello World with http, domain1.lan, port 5555 for registry, port 8080 for UI 

Here's a complete example to test your registry:

```bash
# Pull hello-world from Docker Hub
docker pull hello-world
```

```bash
# Tag for your private registry
docker tag hello-world domain1.lan:5555/hello-world
# Check image is tagged
docker images
```

```bash
# Push to your registry
docker push domain1.lan:5555/hello-world
```

```bash
# Verify in UI
Visit http://domain1.lan:8080 and you should see 'hello-world' listed
```

```bash
# Test pulling
docker rmi hello-world domain1.lan:5555/hello-world
docker pull domain1.lan:5555/hello-world
docker run domain1.lan:5555/hello-world
```
