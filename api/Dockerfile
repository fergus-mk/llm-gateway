# Base stage with common setup
FROM python:3.13-slim as base-stage

WORKDIR /app

# Ensure packages are up to date
RUN apt-get update && \
    apt-get upgrade -y && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Set the venv path to be used in following stages
ENV PATH="/app/venv/bin:$PATH"

# Create a nonroot user who defaults to the group 'nonroot'
RUN useradd nonroot

# Copy the source code
COPY ./src /app

# Build stage for installs
FROM base-stage as build-stage

# Create venv and install requirements
COPY requirements.txt /app 
RUN python -m venv /app/venv && \
    /app/venv/bin/pip install --no-cache-dir -r requirements.txt

# Run stage for running the application
FROM base-stage as run-stage

# Copy the venv from the build-stage
COPY --from=build-stage /app/venv /app/venv

# Assign nonroot user ownership of application code and switch to nonroot user
RUN chown nonroot:nonroot /app
USER nonroot

EXPOSE 8000

# Run our application using the virtual environment's uvicorn
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000", "--reload"]
