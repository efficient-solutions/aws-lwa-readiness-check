# Readiness Check for AWS Lambda Web Adapter

[![Python 3.10+](https://img.shields.io/badge/Python-3.10+-blue.svg)](https://www.python.org/downloads/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://github.com/efficient-solutions/aws-lwa-readiness-check/blob/main/LICENSE)

A lightweight ASGI middleware for handling readiness check requests from [AWS Lambda Web Adapter](https://github.com/awslabs/aws-lambda-web-adapter) (LWA). 

The AWS Lambda Web Adapter allows you to run web applications on AWS Lambda by handling the translation between HTTP APIs and Lambda events.

## Purpose

This middleware helps prevent the `Resource did not stabilize` error during Lambda deployment by responding to the LWA readiness check request before your application completes its initialization. Without proper handling of these requests, the Lambda runtime may fail to start properly.

## Installation

```bash
pip install aws-lwa-readiness-check
```

## Usage

```python
from lwa_readiness.asgi import ReadinessCheckMiddleware

# Wrap your ASGI application
app = ReadinessCheckMiddleware(your_asgi_app)
```

## Configuration

The middleware requires the following environment variable:

- `AWS_LWA_READINESS_CHECK_PATH`: The URL path for readiness check requests

⚠️ **Important**: The default value is `/`, which may conflict with your application's routes. It's recommended to change it to a unique path that your application doesn't use, for example `/lwa-readiness-check`.

## How It Works

The middleware intercepts HTTP requests to the configured path and returns an immediate HTTP 200 response without forwarding the request to your application. All other requests are passed through to your application unchanged.

## Example

### Django

```python
import os
from django.core.asgi import get_asgi_application
from lwa_readiness.asgi import ReadinessCheckMiddleware

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'path.to.django.settings')

application = ReadinessCheckMiddleware(
    get_asgi_application()
)
```

### FastAPI

```python
from fastapi import FastAPI
from lwa_readiness.asgi import ReadinessCheckMiddleware

app = FastAPI()

app.add_middleware(ReadinessCheckMiddleware)
```

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

## Disclaimer

This software product is not affiliated with, endorsed by, or sponsored by Amazon Web Services (AWS) or Amazon.com, Inc. The use of the term "AWS" is solely for descriptive purposes to indicate that the software is compatible with AWS services. Amazon Web Services and AWS are trademarks of Amazon.com, Inc. or its affiliates.