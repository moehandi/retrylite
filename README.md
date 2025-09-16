# retrylite

[![CI](https://github.com/moehandi/retrylite/actions/workflows/ci.yml/badge.svg)](https://github.com/moehandi/retrylite/actions/workflows/ci.yml)
[![codecov](https://codecov.io/gh/moehandi/retrylite/branch/main/graph/badge.svg)](https://codecov.io/gh/moehandi/retrylite)
[![PyPI version](https://badge.fury.io/py/retrylite.svg)](https://badge.fury.io/py/retrylite)
[![Python versions](https://img.shields.io/pypi/pyversions/retrylite.svg)](https://pypi.org/project/retrylite/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Zero Dependencies](https://img.shields.io/badge/deps-none-success.svg)](https://github.com/moehandi/retrylite)

Zero-dependency retry decorator for Python (sync & async) with circuit-breaker, retry-after, and full type safety.

## Features
- Zero runtime dependencies – <b>only std-lib</b>.
- Sync & async support. 
- Exponential backoff & full jitter.
- Circuit-breaker support.  
- Respects Retry-After header.  
- Test coverage & mypy strict. 
- PyPI wheel <b>&lt; 10 kB</b>.

## Installation
```bash
pip install retrylite
```

## Quick Start

### Synchronous

```python
from retrylite import retry
import requests

@retry(max_attempts=3, backoff=1.5)
def fetch(url):
    resp = requests.get(url, timeout=5)
    resp.raise_for_status()
    return resp.json()

try:
    data = fetch("https://httpbin.org/json")
except requests.HTTPError:
    print("Final failure after 3 retries")
```

### Asynchronous

```python
import aiohttp
from retrylite import aretry

@aretry(max_attempts=3, backoff=1.5)
async def fetch(url):
    async with aiohttp.ClientSession() as s:
        async with s.get(url) as resp:
            resp.raise_for_status()
            return await resp.text()

html = asyncio.run(fetch("https://httpbin.org/json"))
```
### Advanced

### Circuit-breaker
```python
from retrylite import retry

@retry(break_on=(ValueError,))
def risky():
    raise ValueError("permanent")  # no retry
```

### Retry-After header
```python
from retrylite import retry
import requests

@retry(retry_after=True)
def api_call():
    resp = requests.get(url)
    resp.raise_for_status()  # respects 503 + Retry-After
```


### Development
```sh
git clone https://github.com/moehandi/retrylite.git
cd retrylite
poetry install
poetry run pre-commit install
poetry run pytest --cov
poetry build
```

### License

MIT - see [LICENSE](LICENSE)