This second diagram focuses on the operating system and the order in which everything starts.

It also shows why installing a Python library is different from starting a server or worker.

# What this diagram teaches

There are three different startup activities:

- Redis starts as a server process.
- Uvicorn starts and loads the FastAPI application.
- Celery starts a worker that imports the task definitions and begins consuming tasks.

These processes are independent. Starting Uvicorn does not automatically start Redis or a 
separately configured Celery worker.

For our Windows example, the worker can be started with:

    celery -A celery_app:celery_app worker --pool=solo --loglevel=info

With the solo pool, tasks execute sequentially in the worker process. With a supported process-based pool, such as 
Celery's prefork pool on supported Unix-like systems, the worker can use child processes to execute tasks concurrently.

The OS schedules runnable threads from these processes on the available CPU cores. A process does not permanently own a 
CPU core simply because it is running.

One subtle point: the initial response and the eventual task result are two different HTTP responses. The first contains 
the task ID; the later response contains the task state or result.

