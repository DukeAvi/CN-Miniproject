# Team 5 : Distributed Job Queue Service

| Socket-based distributed job queue system for reliable, concurrent task execution. Built for the Computer Networks Miniproject.

### Features

- **Robust TCP Communication:** Custom multi-client and multi-worker communication built on standard sockets.
    
- **Secure Data Transfer:** TLS-encrypted connections using self-signed certificates.
    
- **Centralized Job Queue:** Thread-safe job management with proper synchronization locks to prevent race conditions.
    
- **Fault Tolerance & Reliability:** Automated handling of worker failures. If a worker disconnects or crashes mid-task, the server detects the timeout and seamlessly re-queues the job.
    
- **Scalable Concurrency:** Designed to evaluate performance reliably under increasing job loads.
    

### Architecture

**High-level data flow:**

1. Client submits batches of jobs (e.g., math, Fibonacci, sleep) via TLS-encrypted TCP to the server.
    
2. Server manages a thread-safe centralized queue and registers incoming worker nodes.
    
3. Workers periodically request jobs, execute them, and return results to the server while sending heartbeats.
    
4. Server detects crashed/disconnected workers via missed heartbeats and re-queues their tasks to ensure exactly-once processing.
    
5. Client polls the server for completion status and evaluates overall system throughput.
    

**Core components:**

- `server.ipynb`: The central broker. Manages the job queue, registers workers, assigns tasks, and monitors worker health via heartbeats.
    
- `Worker.ipynb`: The execution node. Connects to the server, requests jobs, processes them, and returns results.
    
- `Client.ipynb`: The load generator. Submits batches of tasks to the server and polls for completion status to evaluate system throughput.
    

### Project Structure


``` XML
Distributed-Job-Queue/
|-- server.ipynb
|-- Worker.ipynb
|-- Client.ipynb
`-- README.md
```

### Requirements

- Python 3.x
    
- Jupyter Notebook or JupyterLab
    
- OpenSSL (for generating local certificates)
    

### Running The System

**Step 1: Setup**


``` Bash
git clone git@github.com:DukeAvi/CN-Miniproject.git
cd CN-MiniProject
```

**Step 2: Generate SSL Certificates** To secure the socket connections locally, generate a self-signed certificate and private key from the repo root:

```Bash
openssl req -x509 -newkey rsa:4096 -keyout server.key -out server.crt -days 365 -nodes -subj "/CN=localhost"
```

**Step 3: Boot up** _Note: Open three separate Jupyter Notebook/Lab instances or terminal windows to run these concurrently._

1. **Start the Server:** Open `server.ipynb` and run all cells. Wait for the `Secure TLS Server listening on 0.0.0.0:9999` log.
    
2. **Start the Workers:** Open `Worker.ipynb` and run all cells.
    
    - _To simulate a distributed network, duplicate `Worker.ipynb` (e.g., `Worker_2.ipynb`) and run multiple workers simultaneously._
        
3. **Run the Clients (Load Test):** Open `Client.ipynb` and run all cells. You can adjust the `num_clients` parameter in the final cell to simulate different load conditions.
    

### Message Format

Communications are JSON encoded strings sent over TCP, generally following this shape:


``` JSON
{
	"type": "submit | request | assign | complete",
	"payload": {
        "task": "math",
        "operation": "add",
        "a": 5,
        "b": 10
    },
	"job_id": "uuid-string"
}
```

### Known Limitations

- Uses self-signed certificates.

### Team Members
- Avrit Sharma — PES2UG24CS100  
- B Meghana — PES2UG24CS103  
- Ishanika Vinuthan — PES2UG24CS911  
