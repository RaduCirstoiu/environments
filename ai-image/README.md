# AI Image Lab

This environment provides a containerized workspace for AI development.

## Getting Started

### 1. Create and Run the Container

To build and start the container, use the following command:

```bash
nerdctl run -d \
  --name ai-lab \
  --restart unless-stopped \
  --env-file .env \
  -v $(pwd)/ai-workspace:/workspace \
  ai-lab .
```

**Flags Explanation:**
- `-d`: Runs the container in **detached** mode (in the background).
- `--name ai-lab`: Assigns the name `ai-lab` to the container for easier reference.
- `--restart unless-stopped`: Ensures the container restarts automatically unless it is explicitly stopped.
- `--env-file .env`: Loads environment variables from a local `.env` file into the container.
- `-v $(pwd)/ai-workspace:/workspace`: Mounts the local `ai-workspace` directory to `/workspace` inside the container, ensuring data persistence.
- `ai-lab`: Specifies the image name to use.

### 2. Attach to the Container

To open an interactive shell inside the running container, run:

```bash
nerdctl exec -it ai-lab bash
```

