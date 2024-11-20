# Use Python 3.10 image as base
FROM python:3.10

# Set the working directory in the container
WORKDIR /app

# Install curl and bash
RUN apt-get update -y && \
    apt-get install -y --no-install-recommends \
    curl \
    && rm -rf /var/lib/apt/lists/*

# Install Poetry
RUN curl -sSL https://install.python-poetry.org | python3 -

# Add Poetry to the PATH
ENV PATH="/root/.local/bin:$PATH"

# Copy the rest of the application code into the container
COPY . /app

# Install the dependencies using Poetry
RUN poetry install

# Make sure the prestart script is executable
RUN chmod +x ./prestart.sh

# Expose the port the app runs on
EXPOSE 8000

# Run the prestart script and start the server
CMD ["sh", "-c", "poetry run bash ./prestart.sh && poetry run uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload"]