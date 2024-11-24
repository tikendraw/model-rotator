# Model Rotator

Model Rotator is a Python library for managing multiple LLM (Large Language Model) instances with rate limits and priorities. It dynamically schedules requests to models based on their rate limits, usage, and priority levels, ensuring optimal utilization of available resources.

## Features
* Rate-Limit Management: Automatically tracks and enforces rate limits per model.
* Priority-Based Scheduling: Prioritizes high-priority models over medium and low-priority ones.
* Dynamic Updates: Tracks model usage in real-time and prunes stale usage data.
* Stateful: Maintains state for each model's usage across calls.
* Customizable: Easily configure models with different rate limits and priorities.

## Installation
Install the package from PyPI:

```bash
pip install model-rotator
```

## Usage
1. Define Your Models
Provide a list of model configurations:
> Note: Models listed first will have higher priority if models have same priority given.
```python
from model_rotator import ModelRotator

models = [
    {"name": "groq/llama-3.1-70b-versatile", "priority": "high", "limit": 30},
    {"name": "groq/llama-3.1-70b-specdec", "priority": "high", "limit": 30},
    {"name": "groq/llama-3.1-8b-instant", "priority": "medium", "limit": 30},
    {"name": "groq/llama-3.2-1b-preview", "priority": "low", "limit": 30},
    {"name": "gemini/gemini-1.5-flash", "priority": "medium", "limit": 30},
    {"name": "gemini/gemini-1.5-pro", "priority": "high", "limit": 15},
    {"name": "gemini/gemini-exp-1114", "priority": "high", "limit": 2},
]
```
2. Initialize the Scheduler
```python
rotator = ModelRotator(models)
```

3. Schedule Requests
Use get_next_model() to get the next available model for processing:

```python
for _ in range(50):  # Simulate 50 requests
    model = rotator.get_next_model()
    if model:
        print(f"Using model: {model}")
    else:
        print("All models exhausted, retry later.")
```
4. Check Model States
Inspect the current state of all models:

```python
print(rotator.get_state())
```
## Example Output
```plaintext
Copy code
Request 1: Using model: groq/llama-3.1-70b-versatile
Request 2: Using model: groq/llama-3.1-70b-specdec
...
Request 50: All models exhausted, retry later.

Model States:
[
    {"name": "groq/llama-3.1-70b-versatile", "priority": "high", "limit": 30, "current_usage": 30},
    {"name": "groq/llama-3.1-70b-specdec", "priority": "high", "limit": 30, "current_usage": 30},
    ...
]
```

## API
`ModelRotator(models:Model)`
Initializes the scheduler.

* `models`: A list of dictionaries. Each dictionary must include:
    * `name` (str): The model name.
    * `priority` (str): Priority level (`high`, `medium`, `low`).
    * `limit` (int): Maximum allowed requests per minute.

`get_next_model()`
Returns the name of the next available model based on priority and rate limits.

* Returns:
    * `str`: The model name, or
    * `None` if no models are available.

`get_state()`
Returns the current state of all models, including their usage.

* Returns:
    * `list`: A list of dictionaries with the following fields:
        * `name` (str): Model name.
        * `priority` (str): Priority level.
        * `limit` (int): Rate limit.
        * `current_usage` (int): Current number of requests within the last minute.

## License
This project is licensed under the MIT License. See the LICENSE file for details.

## Contributing
Contributions are welcome! Feel free to open issues or submit pull requests.

