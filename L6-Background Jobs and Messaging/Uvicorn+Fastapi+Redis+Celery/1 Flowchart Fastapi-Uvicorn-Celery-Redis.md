This diagram represents the example we developed: a client submits a task, FastAPI returns a task ID, a Celery 
worker executes the task, and the client retrieves the result through another endpoint.

# How to read this diagram

# Step 1 — The client submits a request.

The client sends an HTTP request to Uvicorn, which serves the FastAPI application.

# Step 2 — FastAPI submits a task.

The route handler executes:

task = greet.delay("Alex")

The Celery client serializes the task information and publishes a message to the configured broker.

# Step 3 — Redis holds the task message.

Redis serves as the message broker. A Celery worker consumes the message when it is available.

# Step 4 — The worker executes the function.

The worker invokes the registered greet function. The function executes in the worker's execution environment, not in the FastAPI request handler.

# Step 5 — The result becomes available.

If Redis is configured as the result backend, Celery stores the task's state and result there.

# Step 6 — FastAPI returns the initial response.

The API returns the task ID without waiting for the background function to finish.

For example:

{
  "task_id": "a3f0c2e1",
  "status": "queued"
}

# Step 7 — The client checks the result.

The client calls GET /tasks/{task_id}. FastAPI uses Celery's result API to retrieve the task state and, when available, the result.

The client and worker do not have to communicate directly. Redis provides the messaging infrastructure between the API and worker.

# Important architectural distinctions
    - Uvicorn and FastAPI: Usually run together in the same Python process. Uvicorn serves HTTP, while FastAPI 
    implements the application routes and request handling.
    - Celery worker: Runs separately from the FastAPI process.
    - Redis broker: Carries task messages between producers and workers.
    - Redis result backend: Stores task states and results when configured.
    - Redis cache: An optional application-level use of Redis. It is not automatically required for Celery.

The three Redis roles can use the same Redis server, as in our example, but they remain logically distinct.

The diagram shows the typical flow rather than every internal implementation detail. Actual task acknowledgement, 
retries, persistence, and failure handling depend on configuration.

