# SiCekCok Data Fetcher - AI Agent Instructions

## Project Overview
This is an asynchronous GraphQL data fetching system for Indonesian student academic records (mahasiswa data). The project queries the SiCekCok university system to extract comprehensive student information including KHS (academic transcript) data.

## Architecture & Data Flow
- **Input**: `data/nim.csv` contains 1,525+ student ID numbers (NIM)
- **Processing**: Jupyter notebook performs async GraphQL queries to `sicekcok.if.unismuh.ac.id/graphql`
- **Output**: Timestamped CSV files in `outputs/` with flattened student + transcript data

The system uses aiohttp for concurrent API calls, dramatically reducing fetch time from sequential to parallel execution.

## Key Components

### Data Structure Pattern
Student records are flattened into wide tables with dynamic KHS columns:
```
nim, nama, kodeProdi, angkatan → khs1_tahunAkademik, khs1_ips, khs1_sksSmt → khs2_... → khs{max}_...
```
The `max_khs` variable dynamically determines column count based on longest transcript found.

### GraphQL Query Pattern
The notebook uses a standardized query structure:
- Variables: `{'nim': String!}`
- Nested response: `mahasiswa { basicInfo, khs { semesterData } }`
- Error handling: Checks for `result.get('data', {}).get('mahasiswa', {})`

## Development Workflows

### Running the Data Fetch
1. Ensure `data/nim.csv` exists with NIM column
2. Execute notebook cells sequentially: `notebooks/async-get-mahasiswa.ipynb`
3. Monitor progress via global counters and async task completion
4. Output automatically saved with timestamp: `mahasiswa_data_YYYYMMDD_HHMMSS.csv`

### Dependencies Management
- Core: `aiohttp` (async HTTP), `pandas` (data processing)
- Optional: `ipywidgets`, `matplotlib`, `torch` (likely for future ML analysis)
- Install via: `%pip install aiohttp pandas` (notebook) or `pip install -r requirements.txt`

## Project-Specific Conventions

### File Organization
- `data/`: Input CSVs (NIM lists)
- `notebooks/`: Jupyter analysis notebooks (Indonesian comments)
- `outputs/`: Generated datasets with timestamps

### Async Pattern
Always use `aiohttp.ClientSession()` context manager with `asyncio.gather()` for bulk operations:
```python
async with aiohttp.ClientSession() as session:
    tasks = [query_graphql(session, nim) for nim in nim_list]
    responses = await asyncio.gather(*tasks)
```

### Data Validation
Include NIM-specific lookups for verification: `df.loc[df['nim'] == nim_yang_dicari]`

## External Dependencies
- **Primary API**: SiCekCok GraphQL endpoint (university system)
- **Data Source**: Indonesian higher education academic records
- **Output Format**: CSV for downstream analysis/ML processing

## Common Patterns
- Global variables track processing state (`max_khs`, `data_list`, `total`)
- Indonesian language documentation and variable names
- Timestamp-based output file naming
- Pandas display options set for wide DataFrames: `pd.set_option('display.max_columns', None)`