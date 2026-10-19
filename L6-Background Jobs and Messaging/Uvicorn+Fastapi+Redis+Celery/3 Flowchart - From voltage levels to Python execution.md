Now let's connect the entire architecture to the hardware model you described.

The following diagram distinguishes physical execution from software abstractions.

# Understanding the layers

# Layer 1: Voltage levels and transistors

At the physical level, electronic circuits use voltage ranges to represent digital states.

Transistors form switching and amplification circuits. These circuits implement logic gates, and combinations of gates 
implement arithmetic units, control logic, and other digital components.

It is useful to think of binary 0s and 1s as the underlying representation, but software is not literally a fixed 
pattern of voltages. The same program can be represented by stored bits and executed through many physical operations.

# Layer 2: CPU, RAM, SSD, and the OS

The SSD stores executable files, Python libraries, and application files.

When a program runs, the OS creates and manages its process, maps the required code and data into virtual memory, 
and arranges for its threads to be scheduled for execution.

The CPU executes instructions, while RAM holds the active code and data needed during execution.

The OS also provides networking, process isolation, and mechanisms through which separate processes communicate.

# Layer 3: Python runtime and libraries

Python is a runtime environment for executing Python programs.

FastAPI, Uvicorn, Celery, and the Redis Python client are software libraries or packages that provide specific capabilities.

Installing the Celery package does not itself create a running Celery worker. Running the Celery command starts the 
worker process, which loads the relevant Python code and begins performing its work.

Likewise, installing the Redis Python client does not start the Redis server.

# Layer 4: Application processes

Our example has three independently running programs:

    - Uvicorn + FastAPI: receives requests and submits tasks.
    - Celery worker: consumes task messages and executes Python functions.
    - Redis server: provides shared data and messaging capabilities.

They communicate using defined interfaces and protocols. Redis commonly communicates with the API and worker over TCP, 
even when all three run on the same computer.

