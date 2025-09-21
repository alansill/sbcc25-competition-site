# Texas Tech University - Team RedRaider

## Diagram

![image](./images/ttu.png)

## Hardware


| Item name                              | Cost per item | Number of items | Total item cost | Link or Description of the item                                                                                                                                                                                                 |
|----------------------------------------|---------------|-----------------|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Radxa SLiM X4L board                   | $178.86       | 1               | $178.86         | [Radxa SLiM X4L board](https://palmshell.io/slim-x4l)                                                                                                                                                                            |
| Radxa X4 boards                        | $83.00        | 20              | $1,660.00       | [Radxa X4 boards](https://radxa.com/products/x/x4)                                                                                                                                                                               |
| Radxa X4 heat sink/stand combinations  | $21.56        | 20              | $431.20         | [Radxa X4 heat sink/stands](https://radxa.com/products/x/x4)                                                                                                                                                                     |
| CR1220 3V Lithium Batteries (4 packs)  | $3.99         | 20              | $15.96          | [3V Lithium Batteries](https://a.co/d/hHNwAPE)                                                                                                                                                                                   |
| Ethernet Switch                        | $64.95        | 1               | $64.95          | [Ethernet Switch](https://winictech.com/poe-switch/1186-poe-sw24p-2g-240.html)                                                                                                                                                    |
| Power supply                           | $13.90        | 22              | $305.80         | [Power supply](https://radxa.com/products/accessories/power-pd-30w/)                                                                                                                                                              |
| Ethernet cables                        | $10.00        | 22              | $220.00         | [Ethernet cables](https://www.amazon.com/Cable-Matters-40Gbps-Rotatable-Ethernet/dp/B0D6YTN8RC?th=1)                                                                                                                              |
| Power meter                            | $65.99        | 1               | $65.99          | [Power meter](https://www.amazon.com/Monitor-Display-Multimeter-Multifunction-Frequency/dp/B0D7L99RHC/ref=asc_df_B0D7L99RHC?mcid=cc974d2d93cf3c68a9d28870593203c2&hvocijid=6890196377341643341-B0D7L99RHC-&hvexpln=73&tag=hyprod-20&linkCode=df0&hvadid=730312820598&hvpos=&hvnetw=g&hvrand=6890196377341643341&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=1026578&hvtargid=pla-2281435179338&psc=1) |
| Case Fan                               | $9.00         | 1               | $9.00           | [Case Fan](https://www.amazon.com/gp/product/B09QT83P3Q)                                                                                                                                                                          |
| Heat sink                              | $19.95        | 1               | $19.95          | [Heat sink](https://www.amazon.com/gp/product/B09QT83P3Q)                                                                                                                                                                         |
| GeekPi 8U Server Rack with 4 extra shelves and blanking panels | $299.91       | 1               | $299.91         | [GeekPi 8U Server Rack](https://www.amazon.com/dp/B0CSCWVTQ7), [Extra shelves](https://www.amazon.com/dp/B0D5XMM7HL), [Blanking panels](https://www.amazon.com/dp/B0D5XKZ714)                                                     |
| Fan Grill 1400mm Guard Black           | $9.99         | 1               | $9.99           | [Fan Grill](https://www.amazon.com/gp/product/B07VCNKDKD)                                                                                                                                                                         |
| 2TB NVMe Drive                         | $189.90       | 1               | $189.90         | [2TB NVMe Drive](https://www.amazon.com/Samsung-970-EVO-Plus-MZ-V7S2T0B/dp/B07MFZXR1B?th=1)                                                                                                                                       |
| NVME Extender                          | $17.39        | 1               | $17.39          | [NVME Extender](https://www.amazon.com/Sintech-M-2-NVME-Extender-NGFF-M-Key-PCIe-3-0-SSD-Extention-Card-with-Anti-electromagnetic-Foiled-Cable-20CMS-Silver-Cable/dp/B07VCNKDKD)                                                  |
| PWM 4-wire fan temperature controller  | $17.99        | 1               | $17.99          | [PWM 4-wire fan temperature controller](https://www.amazon.com/DONGKER-PWM-Driver-Module-DC-12V-24V-48V-2-Channel-4-Wire-PWM-Speed-Display-Module-for-PC-Fan-Alarm/dp/B07VCNKDKD)                                                 |
| **Total**                              |               |                 | **$3,676.88**   |                                                                                                                                                                                                                                  |



## Software
To maximize the performance and efficiency of our cluster, we are utilizing the following software tools:
- MPI Version:
    - OpenMPI – v5.0.5
- Linear Algebra Libraries:
    - OpenBLAS – v0.3.28 for optimized basic linear algebra operations.
- Compilers:
    - GCC – v11.5.0 with optimization flags for x86-based processors.
- Cluster Management Software:
    - SLURM (Simple Linux Utility for Resource Management) for job scheduling and resource allocation.
    - Spack – v24.05.5.0
- Operating System:
    - Rocky Linux 9.5 (Blue Onyx)

## Strategy

### Benchmarks

1. **High Performance Linpack (HPL)**
    - **Installation**: HPL is installed using Spack, and OpenBLAS is also installed using Spack. Dependencies are managed within Spack to ensure compatibility and efficiency.
    - **Configuration**:
      - The problem size (N) is determined using 80% of total RAM while considering overhead. Based on the formula:
         - For Radxa X4 with 8GB RAM, assigning 6GB of the 8GB for HPL, we use N = 38,000.
         - For 20 nodes, N is set to 115,000 in HPL.dat to optimize memory utilization across all nodes.
      - The block size used is 64.
      - For 20 nodes with 4 cores each, we use a process grid of: P = 8, Q = 10.
      - Slurm's CPU binding is used to optimize thread placement and prevent core oversubscription. Example command for running HPL on 80 processes:
         - ```time -p srun --export=ALL -n 80 ./xhpl```
    - **Performance optimization**:
      - Power settings are adjusted via BIOS or dynamically using powercap-info, setting:
         - ```constraint_0_power_limit_uw=5000000 # 5W``` (default is 6W on the X4)
      - The OpenMPI communication between the nodes is adjusted to improve communication overhead.
      - The number of OpenBLAS threads is tuned to optimize computational throughput.

2. **STREAM**
    - **Installation and Compilation**:
      - Installation is done using Spack with GCC, with the source code obtained from the following link:
            - https://www.cs.virginia.edu/stream/FTP/Code/stream.c
      - It is compiled using Spack mpicc:
         - ```mpicc -O3 -fopenmp stream.c -o stream_mpi```
    - **Configuration**:
      - Stream array size is set to the default in the source code, which is 10,000,000.
    - **Execution and Result monitoring**:
      - Slurm is used to gather results and verify consistent numbers across multiple runs.

3. **DLLAMA3**
    - **Installation**:
      - Install using Spack alongside the OpenBLAS already installed for HPL.
      - Using the Spack approach to distribute workloads using MPI.
    - **Configuration**:
      - Matrix sizes are still to be decided.
      - Storing test data on the Head node’s NVMe drive to ensure minimal I/O overhead.
    - **Execution**:
      - The code is executed using SLURM:
         - ```srun --mpi=pmi2 -n 80 ./dlaama3_exe```
      - CPU usage is monitored using Slurm sacct to avoid memory or CPU saturation issues.

### Applications

1. **Hashcat**
    - **Installation**:
      - It is built using Spack install, ensuring a CPU-optimized build.
    - **Configuration**:
      - Using Slurm, the dictionary will be partitioned across the 80 nodes of the cluster, distributing the workload to run on each node.
    - **Performance optimization**:
      - The use of NVMe on the head node as well as the NFS shared file system to avoid memory overheads.
      - SLURM sacct, RAPL, and LIKWID will be used to monitor performance, including temperature and CPU frequency.
      - Thread concurrency: since the Radxa X4 node has 4 cores, we use 4 threads.


## Team Details

**Obianyor, Chidiogo** – Computer Science senior working on undergraduate research in
cybersecurity using a cluster of single board computers (Team Lead).

**Roy Garza** - Computer Engineering freshman.

**Batuhan Sencer** - Computer Science – Junior – Has experience with HPL
Benchmark(optimization).

**Tannar Mitchell** – Computer Science – Freshman – I got into HPC through the Winter Classic
Invitational Student Cluster Competition.

**Ethan Homan** – Computer Science Senior working on undergraduate research in cybersecurity
using a cluster of single board computers.

**Oluwatobiloba Ajani Issac** – Information Technology Freshman.
