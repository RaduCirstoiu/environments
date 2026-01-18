# AI Image Lab

This environment provides a containerized workspace for AI development.

## Getting Started

### 1. Build the Image

Build the Docker image from the Dockerfile:

```bash
nerdctl build -t ai-lab-image .
```

### 2. Run the Container

Start the container in the background:

```bash
nerdctl run -d \
  --name ai-lab \
  --restart unless-stopped \
  --env-file .env \
  -v $(pwd)/ai-workspace:/workspace \
  ai-lab-image
```

**Flags Explanation:**
- `-d`: Runs the container in **detached** mode (in the background).
- `--name ai-lab`: Assigns the name `ai-lab` to the container for easier reference.
- `--restart unless-stopped`: Ensures the container restarts automatically unless it is explicitly stopped.
- `--env-file .env`: Loads environment variables from a local `.env` file into the container.
- `-v $(pwd)/ai-workspace:/workspace`: Mounts the local `ai-workspace` directory to `/workspace` inside the container, ensuring data persistence.
- `ai-lab-image`: Specifies the image name to use (built in Step 1).

### 3. Attach to the Container

To open an interactive shell inside the running container, run:

```bash
nerdctl exec -it ai-lab bash
```