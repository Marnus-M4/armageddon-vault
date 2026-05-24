# Python Snippets & Reference

---

## File Operations

```python
# Read file
with open('file.txt', 'r') as f:
    content = f.read()

# Read lines
with open('file.txt', 'r') as f:
    lines = f.readlines()

# Write file
with open('output.txt', 'w') as f:
    f.write('Hello World')

# Append
with open('log.txt', 'a') as f:
    f.write('new line\n')

# Read JSON
import json
with open('data.json', 'r') as f:
    data = json.load(f)

# Write JSON
with open('output.json', 'w') as f:
    json.dump(data, f, indent=2)

# Walk directory tree
import os
for root, dirs, files in os.walk('/path/to/dir'):
    for file in files:
        print(os.path.join(root, file))
```

---

## HTTP Requests

```python
import requests

# GET
response = requests.get('https://api.example.com/data')
data = response.json()

# POST with JSON
response = requests.post(
    'https://api.example.com/users',
    json={'name': 'Marnus', 'email': 'test@test.com'},
    headers={'Authorization': 'Bearer token123'}
)

# With session (reuse cookies/headers)
session = requests.Session()
session.headers.update({'Authorization': 'Bearer token'})
r = session.get('https://api.example.com/profile')

# Download file
response = requests.get(url, stream=True)
with open('file.zip', 'wb') as f:
    for chunk in response.iter_content(chunk_size=8192):
        f.write(chunk)

# Error handling
try:
    r = requests.get(url, timeout=10)
    r.raise_for_status()
except requests.exceptions.HTTPError as e:
    print(f'HTTP error: {e}')
except requests.exceptions.ConnectionError:
    print('Connection failed')
except requests.exceptions.Timeout:
    print('Request timed out')
```

---

## Security / Networking

```python
import socket
import subprocess

# Port scanner
def scan_port(host, port):
    try:
        sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        sock.settimeout(1)
        result = sock.connect_ex((host, port))
        sock.close()
        return result == 0
    except:
        return False

# Run shell command
result = subprocess.run(['nmap', '-sV', 'target.com'],
                       capture_output=True, text=True)
print(result.stdout)

# Hash a string
import hashlib
md5    = hashlib.md5(b'password').hexdigest()
sha256 = hashlib.sha256(b'password').hexdigest()

# Base64
import base64
encoded = base64.b64encode(b'secret data').decode()
decoded = base64.b64decode(encoded).decode()

# Generate random bytes / token
import secrets
token  = secrets.token_hex(32)      # 64-char hex string
urlsafe = secrets.token_urlsafe(32)  # URL-safe token
```

---

## Data Processing

```python
# List comprehensions
squares     = [x**2 for x in range(10)]
evens       = [x for x in range(20) if x % 2 == 0]
flat        = [item for sublist in nested for item in sublist]

# Dict comprehension
word_lengths = {word: len(word) for word in ['apple', 'banana']}

# Filter / Map / Reduce
from functools import reduce
filtered = list(filter(lambda x: x > 5, [1, 3, 7, 9, 2]))
doubled  = list(map(lambda x: x * 2, [1, 2, 3]))
total    = reduce(lambda a, b: a + b, [1, 2, 3, 4])

# Sort by key
people = [{'name': 'Bob', 'age': 30}, {'name': 'Alice', 'age': 25}]
sorted_people = sorted(people, key=lambda x: x['age'])

# Pandas basics
import pandas as pd
df = pd.read_csv('data.csv')
df.head()
df['column'].value_counts()
df[df['age'] > 30]
df.groupby('category').mean()
df.to_csv('output.csv', index=False)
```

---

## Async / Concurrency

```python
import asyncio
import aiohttp

# Async HTTP requests
async def fetch(session, url):
    async with session.get(url) as response:
        return await response.json()

async def main():
    async with aiohttp.ClientSession() as session:
        tasks = [fetch(session, url) for url in urls]
        results = await asyncio.gather(*tasks)

asyncio.run(main())

# Threading for I/O
from concurrent.futures import ThreadPoolExecutor
with ThreadPoolExecutor(max_workers=10) as executor:
    results = list(executor.map(process_item, items))
```

---

## Useful One-Liners

```python
# Flatten a list
flat = sum([[1,2],[3,4]], [])

# Most common element
from collections import Counter
most_common = Counter(lst).most_common(1)[0][0]

# Chunk a list into groups of n
chunks = [lst[i:i+n] for i in range(0, len(lst), n)]

# Remove duplicates preserving order
seen = set(); unique = [x for x in lst if not (x in seen or seen.add(x))]

# Current timestamp
from datetime import datetime
ts = datetime.now().strftime('%Y-%m-%d %H:%M:%S')

# Pretty print JSON
import json; print(json.dumps(data, indent=2))

# Quick HTTP server
# python -m http.server 8080

# Timer
import time
start = time.time()
# ... code ...
print(f'Elapsed: {time.time() - start:.2f}s')
```

---

## Environment Variables & Config

```python
import os
from dotenv import load_dotenv

load_dotenv()  # loads .env file

API_KEY    = os.getenv('API_KEY', 'default_value')
DEBUG_MODE = os.getenv('DEBUG', 'false').lower() == 'true'

# argparse — CLI arguments
import argparse
parser = argparse.ArgumentParser(description='My tool')
parser.add_argument('target', help='Target host')
parser.add_argument('-p', '--port', type=int, default=80)
parser.add_argument('-v', '--verbose', action='store_true')
args = parser.parse_args()
print(args.target, args.port, args.verbose)
```
