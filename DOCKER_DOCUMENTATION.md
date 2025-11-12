# Docker - Documentation

This document contains all documentation extracted from Docker MCP server and official Docker documentation.

**Last Updated:** November 12, 2025

**Source:** https://docs.docker.com/

**Documentation:** https://docs.docker.com/

---

## Table of Contents

1. [Introduction](#introduction)
2. [System Information](#system-information)
3. [Container Management](#container-management)
4. [Container Operations](#container-operations)
5. [Logs and Monitoring](#logs-and-monitoring)
6. [System Commands](#system-commands)
7. [API Reference](#api-reference)

---

## Introduction

Docker is a platform for developing, shipping, and running applications using containerization. Docker enables you to separate your applications from your infrastructure so you can deliver software quickly.

### Key Concepts

- **Containers**: Lightweight, portable units that package applications and their dependencies
- **Images**: Read-only templates used to create containers
- **Dockerfile**: Text file containing instructions for building images
- **Docker Compose**: Tool for defining and running multi-container Docker applications

---

## System Information

### Docker System Info

Get comprehensive system information about the Docker installation:

**MCP Function:** `mcp_docker_system_info`

**Response includes:**
- Container counts (running, paused, stopped)
- Image count
- Storage driver information
- Network plugins
- Volume plugins
- Logging drivers
- Memory and CPU limits
- Kernel version
- Operating system details
- Runtime information

**Example Response Structure:**

```json
{
  "ID": "71344b5f-3dfa-4811-8c2f-c6babfb008e4",
  "Containers": 12,
  "ContainersRunning": 11,
  "ContainersPaused": 0,
  "ContainersStopped": 1,
  "Images": 10,
  "Driver": "overlayfs",
  "MemoryLimit": true,
  "SwapLimit": true,
  "CpuCfsPeriod": true,
  "CpuCfsQuota": true,
  "CPUShares": true,
  "CPUSet": true,
  "PidsLimit": true,
  "IPv4Forwarding": true,
  "Debug": false,
  "SystemTime": "2025-11-12T05:29:21.198097356Z",
  "LoggingDriver": "json-file",
  "CgroupDriver": "cgroupfs",
  "CgroupVersion": "2",
  "KernelVersion": "6.6.87.2-microsoft-standard-WSL2",
  "OperatingSystem": "Docker Desktop",
  "OSType": "linux",
  "Architecture": "x86_64",
  "NCPU": 8,
  "MemTotal": 8286064640,
  "ServerVersion": "28.5.1"
}
```

### Docker Version

Get Docker version information:

**MCP Function:** `mcp_docker_system_version`

**Response includes:**
- Platform name and version
- Engine version and details
- containerd version
- runc version
- docker-init version
- API version
- Git commit
- Go version
- Build time

**Example Response:**

```json
{
  "Platform": {
    "Name": "Docker Desktop 4.48.0 (207573)"
  },
  "Components": [
    {
      "Name": "Engine",
      "Version": "28.5.1",
      "Details": {
        "ApiVersion": "1.51",
        "Arch": "amd64",
        "BuildTime": "2025-10-08T12:17:24.000000000+00:00",
        "Experimental": "false",
        "GitCommit": "f8215cc",
        "GoVersion": "go1.24.8",
        "KernelVersion": "6.6.87.2-microsoft-standard-WSL2",
        "MinAPIVersion": "1.24",
        "Os": "linux"
      }
    }
  ],
  "Version": "28.5.1",
  "ApiVersion": "1.51"
}
```

---

## Container Management

### List Containers

List all containers with their status, names, and basic info:

**MCP Function:** `mcp_docker_container_list`

**Parameters:**
- `all` (boolean, optional): Show all containers (default: false - only running)
- `filters` (object, optional): Filter containers by labels, status, etc.

**Example Usage:**

```python
# List running containers
containers = mcp_docker_container_list(all=False)

# List all containers including stopped
all_containers = mcp_docker_container_list(all=True)

# Filter by status
filtered = mcp_docker_container_list(
    filters={"status": ["running"]}
)
```

**Response includes:**
- Container ID
- Names
- Image
- Status
- Ports
- Created timestamp
- Size

### Inspect Container

Get detailed information about a specific container:

**MCP Function:** `mcp_docker_container_inspect`

**Parameters:**
- `containerId` (string, required): Container ID or name

**Example Usage:**

```python
info = mcp_docker_container_inspect(containerId="my-container")
```

**Response includes:**
- Container configuration
- Network settings
- Mounts
- Environment variables
- Resource limits
- State information
- Log path

---

## Container Operations

### Start Container

Start a stopped container:

**MCP Function:** `mcp_docker_container_start`

**Parameters:**
- `containerId` (string, required): Container ID or name

**Example Usage:**

```python
mcp_docker_container_start(containerId="my-container")
```

### Stop Container

Stop a running container:

**MCP Function:** `mcp_docker_container_stop`

**Parameters:**
- `containerId` (string, required): Container ID or name
- `timeout` (number, optional): Timeout in seconds (default: 10)

**Example Usage:**

```python
# Stop with default timeout
mcp_docker_container_stop(containerId="my-container")

# Stop with custom timeout
mcp_docker_container_stop(containerId="my-container", timeout=30)
```

### Restart Container

Restart a container:

**MCP Function:** `mcp_docker_container_restart`

**Parameters:**
- `containerId` (string, required): Container ID or name
- `timeout` (number, optional): Timeout in seconds (default: 10)

**Example Usage:**

```python
mcp_docker_container_restart(containerId="my-container")
```

---

## Logs and Monitoring

### Container Logs

Retrieve logs from a Docker container with optional filtering:

**MCP Function:** `mcp_docker_container_logs`

**Parameters:**
- `containerId` (string, required): Container ID or name
- `tail` (number, optional): Number of lines to retrieve from the end (default: 100)
- `since` (string, optional): Show logs since timestamp or relative time (e.g., '10m', '1h', '24h')
- `until` (string, optional): Show logs until timestamp or relative time
- `timestamps` (boolean, optional): Include timestamps in output (default: false)

**Example Usage:**

```python
# Get last 100 lines
logs = mcp_docker_container_logs(containerId="my-container")

# Get last 50 lines
logs = mcp_docker_container_logs(containerId="my-container", tail=50)

# Get logs from last hour
logs = mcp_docker_container_logs(
    containerId="my-container",
    since="1h"
)

# Get logs with timestamps
logs = mcp_docker_container_logs(
    containerId="my-container",
    timestamps=True
)

# Get logs between two times
logs = mcp_docker_container_logs(
    containerId="my-container",
    since="2025-11-12T00:00:00Z",
    until="2025-11-12T12:00:00Z"
)
```

**Time Format Examples:**
- `"10m"` - 10 minutes ago
- `"1h"` - 1 hour ago
- `"24h"` - 24 hours ago
- `"2025-11-12T00:00:00Z"` - ISO 8601 timestamp

---

## System Commands

### Docker System Information

Get Docker system information including:
- Container and image counts
- Storage driver
- Network and volume plugins
- Memory and CPU configuration
- Runtime information

**MCP Function:** `mcp_docker_system_info`

**Example Usage:**

```python
system_info = mcp_docker_system_info()
```

### Docker Version

Get Docker version information including:
- Platform version
- Engine version
- containerd version
- runc version
- API version

**MCP Function:** `mcp_docker_system_version`

**Example Usage:**

```python
version_info = mcp_docker_system_version()
```

---

## API Reference

### Container Management Functions

#### `mcp_docker_container_list`
List all containers with their status.

**Parameters:**
- `all` (boolean): Show all containers including stopped
- `filters` (object): Filter containers by labels, status, etc.

**Returns:** Array of container objects

#### `mcp_docker_container_inspect`
Get detailed information about a container.

**Parameters:**
- `containerId` (string): Container ID or name

**Returns:** Container inspection object

#### `mcp_docker_container_start`
Start a stopped container.

**Parameters:**
- `containerId` (string): Container ID or name

**Returns:** Success status

#### `mcp_docker_container_stop`
Stop a running container.

**Parameters:**
- `containerId` (string): Container ID or name
- `timeout` (number): Timeout in seconds

**Returns:** Success status

#### `mcp_docker_container_restart`
Restart a container.

**Parameters:**
- `containerId` (string): Container ID or name
- `timeout` (number): Timeout in seconds

**Returns:** Success status

#### `mcp_docker_container_logs`
Retrieve logs from a container.

**Parameters:**
- `containerId` (string): Container ID or name
- `tail` (number): Number of lines from end
- `since` (string): Show logs since timestamp/relative time
- `until` (string): Show logs until timestamp/relative time
- `timestamps` (boolean): Include timestamps

**Returns:** Log output string

### System Functions

#### `mcp_docker_system_info`
Get Docker system information.

**Parameters:** None

**Returns:** System information object

#### `mcp_docker_system_version`
Get Docker version information.

**Parameters:** None

**Returns:** Version information object

---

## Common Use Cases

### Monitoring Container Health

```python
# List all running containers
containers = mcp_docker_container_list(all=False)

# Check each container's status
for container in containers:
    info = mcp_docker_container_inspect(containerId=container['id'])
    print(f"Container: {container['name']}")
    print(f"Status: {info['State']['Status']}")
    print(f"Health: {info['State'].get('Health', {}).get('Status', 'N/A')}")
```

### Viewing Recent Logs

```python
# Get logs from last 30 minutes
logs = mcp_docker_container_logs(
    containerId="my-app",
    since="30m",
    timestamps=True
)
print(logs)
```

### Restarting Failed Containers

```python
# List all containers
containers = mcp_docker_container_list(all=True)

# Find stopped containers and restart them
for container in containers:
    if container['status'].startswith('Exited'):
        mcp_docker_container_start(containerId=container['id'])
        print(f"Restarted: {container['name']}")
```

### Checking System Resources

```python
# Get system information
info = mcp_docker_system_info()

print(f"Total Containers: {info['Containers']}")
print(f"Running: {info['ContainersRunning']}")
print(f"Stopped: {info['ContainersStopped']}")
print(f"Total Images: {info['Images']}")
print(f"Memory Limit: {info['MemoryLimit']}")
print(f"CPU Shares: {info['CPUShares']}")
```

---

## Troubleshooting

### Container Won't Start

1. Check container logs:
```python
logs = mcp_docker_container_logs(containerId="my-container")
```

2. Inspect container configuration:
```python
info = mcp_docker_container_inspect(containerId="my-container")
```

3. Check system resources:
```python
system_info = mcp_docker_system_info()
```

### Logs Not Appearing

1. Verify container is running:
```python
containers = mcp_docker_container_list(all=False)
```

2. Check log driver:
```python
system_info = mcp_docker_system_info()
print(f"Logging Driver: {system_info['LoggingDriver']}")
```

3. Try with timestamps:
```python
logs = mcp_docker_container_logs(
    containerId="my-container",
    timestamps=True
)
```

### Performance Issues

1. Check system resources:
```python
info = mcp_docker_system_info()
print(f"Memory: {info['MemTotal']} bytes")
print(f"CPUs: {info['NCPU']}")
```

2. Monitor container resource usage:
```python
info = mcp_docker_container_inspect(containerId="my-container")
# Check resource limits in info['HostConfig']
```

---

## Best Practices

1. **Use Container Names**: Prefer container names over IDs for readability
2. **Set Timeouts**: Always set appropriate timeouts for stop/restart operations
3. **Monitor Logs**: Regularly check container logs for issues
4. **Resource Limits**: Set appropriate resource limits for containers
5. **Health Checks**: Implement health checks for critical containers
6. **Log Rotation**: Configure log rotation to prevent disk space issues

---

## Additional Resources

- **Docker Documentation:** https://docs.docker.com/
- **Docker Hub:** https://hub.docker.com/
- **Docker Compose:** https://docs.docker.com/compose/
- **Dockerfile Reference:** https://docs.docker.com/reference/dockerfile/

---

*End of Documentation*








