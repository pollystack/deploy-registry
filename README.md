# Private Docker Registry

A self-hosted Docker registry with a web UI.

## Prerequisites

- Docker
- Docker Compose
- Existing SSL certificates

## Directory Structure

```
docker-registry/
├── auth/
│   └── htpasswd
├── certs/
│   └── domain1/
│       ├── fullchain.pem
│       └── privkey.pem
├── data/
├── docker-compose.yml
└── README.md
```

## Setup Instructions

1. Create required directories:
    ```bash
       mkdir -p auth certs data
    ```
2. Create authentication credentials:
    ```bash
      # Note: Replace 'admin' and 'yourpassword' with your desired credentials.
      docker run --entrypoint htpasswd httpd:2 -Bbn admin yourpassword > auth/htpasswd
    ```
    These same credentials need to be updated in docker-compose.yml for the registry-ui service.

3. Place your SSL certificates:

    Place your fullchain.pem and privkey.pem in the appropriate directory under certs/
    Update the certificate paths in docker-compose.yml if your structure differs

4. Start the registry:
    ```bash
    docker-compose up -d
    ```

## Usage

Scroll down for a Practical Example using helloworld to test your new registry

### Login to Registry

```bash
docker login domain1.lan:5555
```

### Push Images
```bash
# Tag your image
docker tag your-image:tag domain1.lan:5555/your-image:tag
# Push to registry
docker push domain1.lan:5555/your-image:tag
```

### Pull Images
```bash
docker pull domain1.lan:5555/your-image:tag
```
## Web Interface
Access the registry UI at: http://domain1.lan:8080

## Default Credentials
```
Username: admin (or as set in htpasswd)
Password: yourpassword (or as set in htpasswd)
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

## Practical Example Using Hello World

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