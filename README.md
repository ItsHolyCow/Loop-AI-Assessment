# Loop-AI-Assessment
The required files for the company Loop.ai's assignment. To be evaluated.

This project implements a priority-aware, rate-limited, asynchronous Data Ingestion API System using FastAPI. It supports:

Submitting ingestion jobs containing a list of IDs with a priority (HIGH, MEDIUM, LOW)

Asynchronous batch processing (max 3 IDs per batch)

Rate limiting: Only one batch every 5 seconds

Priority queue: Higher priority jobs are processed first, based on (priority, created_time)

Status endpoint to track each job and its batches

🛠️ Tech Stack
Component	Tool/Library
Web framework	FastAPI
Async runtime	asyncio
Task queue	In-memory + deque
Unique IDs	uuid module
Data storage	In-memory dictionaries
Testing	pytest + TestClient
Hosting Ready	Can be deployed on Render, Railway, Heroku, etc.

✅ Features Implemented
1. POST /ingest
Accepts JSON with ids (list[int]) and priority (enum).

Slices ids into batches of 3.

Queues batches by priority and creation time.

Triggers asynchronous batch processing.

Returns a unique ingestion_id.

2. GET /status/{ingestion_id}
Fetches the ingestion request and batch statuses.

Response shows:

ingestion_id

Overall ingestion status (derived from batch statuses)

Each batch's ids, batch_id, and individual status

3. Batch Processor
Handles one batch every 5 seconds

Simulates external API call per ID (1 sec delay each)

Respects priority + FIFO order

Ensures no concurrent processing via asyncio.Lock

📋 Status Logic
yet_to_start: All batches haven't started.

triggered: At least one batch has started.

completed: All batches are done.

📂 Files in Repository
File	Purpose
main.py	Core FastAPI app with endpoints and logic
test_app.py	Contains 3 end-to-end tests covering priorities, rate-limiting, and status correctness
README.md	You’re reading it! Setup, features, design decisions

🧪 Testing Highlights
Tests are run using pytest:

test_ingestion_and_status:

Posts high and medium jobs

Asserts correct ingestion ID, and status transitions

test_batch_priority_order:

Checks if a later HIGH priority job jumps ahead of earlier MEDIUM one

test_batch_rate_limit:

Ensures batches are delayed by 5 seconds each

Verifies one batch completes before the next starts

To run:

bash
Copy
Edit
pytest test_app.py -v
🚀 How to Run Locally
Install dependencies

bash
Copy
Edit
pip install fastapi uvicorn
Start server

bash
Copy
Edit
uvicorn main:app --reload
Test with curl or Postman

bash
Copy
Edit
curl -X POST http://127.0.0.1:8000/ingest -H "Content-Type: application/json" -d '{"ids":[1,2,3], "priority": "HIGH"}'
💡 Design Notes
All storage is in-memory for simplicity.

If hosted, use a background worker queue (e.g., Celery or Redis) in production.

Rate-limiting enforced by asyncio.sleep(5) per batch.

Safe for concurrent requests due to asyncio.Lock.
