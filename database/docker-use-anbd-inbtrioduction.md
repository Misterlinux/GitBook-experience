# DOCKER use anbd inbtrioduction

* 1
* 1
* 1
* 1

The PostgreSQL is a database server running as a background service, it can receive\
SQL commands on its dedictaed network port.\
The DBeaver is a database client that connects to the PostgresSQL server, it acts as an interface\
that doesn't store the data itself.

1

The Operating System is organized into abstraction layers that allow for modular development,\
translating high-level user commands into hardware-compatible instructions.

> User Space: The top layer where client interfaces and user applications run.> \
> System Call Interface: The layer that receives and routes application requests.> \
> Kernel: The layer that verifies permissions and translates requests into hardware driver commands.> \
> Hardware: The physical base layer that executes disk reads and hardware tasks,> \
> sending interrupt signals back up to the kernel to be processed for application processes.

1

The hypervisor, also called a Virtual Machine Monitor (VMM), is the abstraction layer that provides\
the environment necessary to run virtual machines.\
It's linked to the host machine's boot process, it starts automatically when the computer turns on.\
The Virtualization is teh process that abstracts local physical\
resources, like CPU cores, RAM, and storage, and allocates them to isolated virtual machines.

1

> The hypervisor creates a virtual CPU (vCPU) proxy that manages and schedules CPU time slices> \
> across different virtual machines.> \
> A distinct virtual memory segment is allocated to each VM to eliminate the risk> \
> of unauthorized memory access between machines.> \
> The hypervisor implements dedicated virtual disk files (such as VHDX or VMDK)> \
> to act as physical hard drives for the VMs. These contain the virtual machine's filesystem, binaries,> &#x20;and system libraries.> \
> The Virtual network adapters allow VMs to connect to the physical host network through> \
> software-based virtual switches, eliminating the need for a dedicated physical network card for each VM.

1

The hypervisor can apply different levels of virtualization. They vary based on how many operations\
need to be emulated by software compared to how many are passed directly to the physical hardware.\
The different levels of virtualization depend on how the hypervisor processes Guest OS commands:

1

The hypervisor acts as a traffic controller for guest OS commands. The virtualization methods describe how the hypervisor intercepts, translates, or passes operations for each VM component:

* Software-Based Full Virtualization: It's the default process for guest OS commands.  \
  The hypervisor intercepts and binary-translates the instructions at runtime into compatible  \
  hardware operations, generating high CPU overhead.
* Hardware-Assisted Virtualization: The guest OS instructions execute directly on physical host processors  \
  equipped with dedicated virtualization circuits.  \
  It offers lower overhead but requires specific processors with virtualization support.
* Paravirtualization: The Guest OS sends direct requests to the hypervisor called hypercalls, which are  \
  compatible with the host. This allows the virtualization layer to skip heavy emulation steps but requires  \
  a Guest OS specifically designed to work in a virtualized environment.

1

Virtual machines and WSL2-based Docker containers both rely on a hypervisor environment to\
execute their commands.

Virtual Machines: They rely on software-abstracted hardware. Each VM runs its own independent kernel&#x20;with a full, isolated operating system.                                                                                                      Docker Containers: These are software-isolated processes running on real hardware. They share a single&#x20;host-provided WSL2 kernel, making them faster in their Windows-compatible implementation.

1

1

1

1

1

1

1

1

1

1

1
