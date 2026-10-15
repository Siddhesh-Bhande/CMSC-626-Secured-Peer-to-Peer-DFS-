# Secured Peer-to-Peer Distributed File System (P2P-DFS)

A secure, decentralized distributed file system implemented in Java. The system incorporates hybrid cryptographic protocols (AES and RSA), role-based access controls, and master-node metadata indexing to guarantee confidentiality, authenticity, and data availability across networked peers.

## Architecture

```
                    ┌─────────────────────────┐
                    │      Master Server      │
                    │ (Metadata, Auth, ACL)   │
                    └────────────┬────────────┘
                                 │ Token & Peer Directory
            ┌────────────────────┼────────────────────┐
            │                    │                    │
            ▼                    ▼                    ▼
     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
     │  Peer Node 1 │     │  Peer Node 2 │     │  Peer Client │
     │ (Block Store)│     │ (Block Store)│     │(Crypt Engine)│
     └──────────────┘     └──────────────┘     └──────────────┘
```

The system separates metadata management from distributed block storage:
1. **Master Node (`MasterServer.java`, `Master.java`)**: Maintains file namespace metadata, peer node heartbeat directories, user authentication registries (`users.txt`), and access control permissions.
2. **Peer Storage Nodes (`PeerServer.java`)**: Store encrypted file chunks across distributed peer directories, responding to verified retrieval queries.
3. **Peer Client (`PeerClient.java`, `FDS.java`)**: Authenticates users, chunks files, coordinates symmetric encryption, submits file index requests to the master, and disperses chunks to designated peers.

## Security Engineering

- **Hybrid Cryptography**: Employs AES-256 for high-speed symmetric data encryption (`AESEncrypt.java`) combined with RSA public-key cryptography (`RSA.java`) for secure key exchange and authentication handshake tokens.
- **Granular Permissions (`Permissions.java`, `PermissionsImpl.java`)**: Enforces read, write, and execute access matrices per user and per file before issuing block-transfer authorization tokens.
- **Data Integrity & Verification**: Block-level checksums prevent man-in-the-middle tampering during chunk transport.

## Project Structure

```
CMSC-626-Secured-Peer-to-Peer-DFS-/
├── docs/
│   └── P2P_DFS_Project_Team7_Report.pdf  # Comprehensive academic research report and benchmarks
├── resources/
│   ├── benchmark.properties              # Latency and throughput benchmark configs
│   ├── config.properties                 # Network port and host configurations
│   └── users.txt                         # User authentication directory
├── src/
│   ├── AESEncrypt.java                   # Symmetric AES encryption implementation
│   ├── RSA.java                          # Asymmetric RSA key pair and handshake logic
│   ├── MasterServer.java                 # Master metadata and auth daemon
│   ├── Master.java                       # Master coordination engine
│   ├── MasterQuery.java                  # Protocol parser for master RPCs
│   ├── PeerServer.java                   # Distributed peer block daemon
│   ├── PeerClient.java                   # Client interface and chunk distributor
│   ├── Permissions.java                  # Remote permissions interface
│   ├── PermissionsImpl.java              # Access control logic implementation
│   ├── fileAccessQuery.java              # File lookup protocol query handlers
│   ├── benchmarking.java                 # Performance and latency evaluation harness
│   └── FDS.java                          # Main entry point and CLI coordinator
├── .gitignore                            # Java class and build artifact rules
├── LICENSE                               # MIT License
└── README.md                             # Case-study documentation and architecture
```

## Compilation and Execution

### Prerequisites
- Java Development Kit (JDK) 11 or higher

### Build
Compile all source files from the project root:
```bash
javac -d bin -cp resources src/*.java
```

### Run Master Node
```bash
java -cp bin:resources MasterServer
```

### Run Peer Storage Nodes
```bash
java -cp bin:resources PeerServer <port>
```

### Run Peer Client Interface
```bash
java -cp bin:resources FDS
```

## Evaluation and Benchmarks

Detailed performance benchmarks assessing throughput, network latency under varying file sizes, and cryptographic overhead are documented in [docs/P2P_DFS_Project_Team7_Report.pdf](docs/P2P_DFS_Project_Team7_Report.pdf).

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
