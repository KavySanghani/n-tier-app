# N-Tier Library Management Application

This is a 3-tier Library Management application demonstrating a clear separation of concerns across architectural layers.

## How to Run the Application

### Prerequisites
Make sure you have Python 3.8+ installed. You also need FastAPI and Uvicorn.
```bash
pip install fastapi uvicorn pydantic pytest
```

### Starting the Server
From the root directory, start the FastAPI server with Uvicorn:
```bash
uvicorn presentation.app:app --reload
```
The API will be available at `http://127.0.0.1:8000`. You can explore the endpoints interactively via the Swagger UI at `http://127.0.0.1:8000/docs`.

### Running Tests
To run the business tier unit tests using `pytest`:
```bash
pytest tests/
```

To run the swap test demonstrating loose coupling:
```bash
python swap_test.py
```

## Architecture Diagram

```text
+---------------------+
|                     |
|  Presentation Tier  | (FastAPI, REST Endpoints, Routing)
|                     |
+---------+-----------+
          | (DTOs/Models)
          v
+---------+-----------+
|                     |
|    Business Tier    | (BookManager, Validation, Rules)
|                     |
+---------+-----------+
          | (Repository Interface)
          v
+---------+-----------+
|                     |
|      Data Tier      | (SQLiteBookRepository, InMemoryRepo)
|                     |
+---------------------+
```

## Tier Descriptions

1. **Presentation Tier (`/presentation`)**: Handles input, output, and HTTP routing. It parses JSON payloads and serializes responses. It does not implement any business logic or direct database queries.
2. **Business Logic Tier (`/business`)**: The core of the application. It orchestrates the application flows and enforces all business validation rules (like valid ISBNs, quantities, and dates). It is entirely decoupled from the database or UI.
3. **Data Access Tier (`/data`)**: Strictly handles data persistence and retrieval. It implements specific drivers (like SQLite) but contains no business validation logic.

## Design Decision: The Repository Pattern

To achieve true loose coupling between the Business and Data tiers, I utilized the **Repository Interface Pattern**. The Business logic (`BookManager`) relies exclusively on an abstract `BookRepository` class rather than importing `sqlite3` directly. This decision is critical because it allowed us to effortlessly swap the actual data source. For example, during unit testing and in our `swap_test.py` script, we were able to inject an `InMemoryBookRepository` (a fake data source using standard Python dictionaries). This means the business logic could be rigorously tested in isolation without hitting a real database or changing a single line of business tier code, perfectly proving the resilience of the 3-tier architecture.