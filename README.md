AI Assistant

# Ollama Server with Qwen3-Coder 8K Model

This repository contains a Docker Compose setup for running an Ollama server with the Qwen3-Coder 8K model. The setup includes Traefik reverse proxy configuration with basic authentication.

## Prerequisites

- Docker and Docker Compose installed
- An existing Traefik network (e.g., `proxy`) - you can replace this with your own Traefik network

## Installation

### 1. Create the Traefik Network

Before starting, ensure you have a Traefik network available:

```shell script
docker network create proxy
```


You can replace `proxy` with any existing Traefik network in your environment.

### 2. Configure Environment Variables

Create a `.env` file based on the example:

```shell script
HOST_URL=example.com
USERS=user1:pw-hash1,user2:pw-hash2
```


### 3. Start the Services

```shell script
docker-compose up -d
```


## Model Setup

The configuration uses a custom Modelfile (`Modelfile8k`) to load the Qwen3-Coder model with 8K context window:

```dockerfile
FROM qwen3-coder:latest

PARAMETER num_ctx 8192
PARAMETER temperature 0.2
```

Now, you need to create the 8k model  
```shell
docker exec ollama ollama create qwen3-coder-8k -f /tmp/Modelfile

```

and then run it

```shell
docker exec ollama ollama run qwen3-coder-8k:latest
```


## Testing the Installation

### 1. Check Container Status

```shell script
docker-compose ps
```


### 2. Test Ollama API

```shell script
curl -X POST http://localhost:11434/api/generate \
  -H "Content-Type: application/json" \
  -d '{
    "model": "qwen3-coder",
    "prompt": "Hello, how are you?",
    "stream": false
  }'
```


### 3. Verify Model Loading

```shell script
curl http://localhost:11434/api/tags
```


## Monitoring

### Check Resource Usage

```shell script
docker stats ollama
```


### Monitor Logs

```shell script
docker-compose logs -f
```


### Check Disk Usage

```shell script
du -sh data/
```


## Configuration Details

- **Model**: Qwen3-Coder with 8K context window
- **Port**: 11434 (accessible via Traefik)
- **Authentication**: Basic authentication configured in Traefik
- **Network**: Uses external `proxy` network (replaceable with your own Traefik network)

## Security Notes

- The `.env` file contains sensitive information and should not be committed to version control
- Ensure your Traefik network is properly secured
- Update the user credentials in the `.env` file for production use

## Customization

To use a different Traefik network, modify the `networks` section in `docker-compose.yml`:

```yaml
networks:
  your-network-name:
    external: true
```


And update the corresponding references in the labels and networks sections.

## Troubleshooting

### Common Issues

1. **Network not found**: Ensure the Traefik network exists before starting services
2. **Authentication failed**: Verify credentials in `.env` file
3. **Model loading issues**: Check that `qwen3-coder:latest` image is available locally or can be pulled

### Useful Commands

```shell script
# View container logs
docker-compose logs ollama

# Restart service
docker-compose restart ollama

# Stop all services
docker-compose down

# Start all services
docker-compose up -d
```


This setup provides a production-ready Ollama server with the Qwen3-Coder 8K model, accessible through Traefik with basic authentication.