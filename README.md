# Memory-Efficient Versioned File Indexer
 
A C++ command-line tool that builds word-frequency indexes over large text/log files using a **fixed-size buffer**, ensuring memory usage stays independent of file size. Supports multiple file **versions** and three query types: word count, version diff, and top-K frequent words.
 
## Features
 
- **Streaming, buffer-based file reading** — never loads an entire file into memory
- **Boundary-safe tokenization** — correctly reconstructs words split across buffer reads
- **Versioned indexing** — maintain and query multiple file versions in a single run
- **Three query types**:
  - `word` — frequency of a word in a given version
  - `diff` — frequency difference of a word between two versions
  - `top` — top-K most frequent words in a version
- **Configurable buffer size** between 256 KB and 1024 KB
- Reports execution time and allocated buffer size for every run
## Design
 
The project follows an object-oriented design with clear separation of concerns:
 
| Class | Responsibility |
|---|---|
| `BufferedFileReader` | Reads the file incrementally in fixed-size chunks |
| `Tokenizer` | Extracts case-insensitive, alphanumeric word tokens, handling tokens split across buffer boundaries |
| `VersionedIndexer` | Builds and stores a word → frequency map for each version |
| `QueryProcessor` | Executes word, diff, and top-K queries against one or more indexes |
 
### C++ Concepts Demonstrated
 
- **Abstraction & Inheritance** — abstract base class with derived query/index classes
- **Runtime Polymorphism** — virtual functions and dynamic dispatch for query execution
- **Function Overloading** — multiple overloads with different parameter lists
- **Exception Handling** — `try` / `catch` / `throw` for runtime error handling (e.g. missing files, invalid args)
- **Templates** — a generic user-defined template used in the indexing/query logic
## Memory Model
 
- Memory usage grows **only with the number of unique words**, not with file size
- The file is streamed through a constant-size buffer (256 KB – 1024 KB) for the entire execution
- No growing in-memory copy of file contents is ever maintained
## Build
 
```bash
g++ -std=c++17 -O2 -o analyzer rollnumber_firstname.cpp
```
 
## Usage
 
```bash
./analyzer --file <path> --version <name> --buffer <kb> --query <type> [options]
```
 
### Command-Line Arguments
 
| Flag | Description |
|---|---|
| `--file <path>` | Input file path (single-version queries) |
| `--file1 <path>` | First input file (diff query) |
| `--file2 <path>` | Second input file (diff query) |
| `--version <name>` | Version identifier (single-version queries) |
| `--version1 <name>` | First version identifier (diff query) |
| `--version2 <name>` | Second version identifier (diff query) |
| `--buffer <kb>` | Buffer size in KB (256–1024) |
| `--query <type>` | Query type: `word` \| `diff` \| `top` |
| `--word <token>` | Target word for `word` / `diff` queries |
| `--top <k>` | Number of top results for `top` query |
 
### Examples
 
**Word count query**
```bash
./analyzer --file dataset_v1.txt --version v1 \
  --buffer 512 --query word --word error
```
 
**Top-K query**
```bash
./analyzer --file dataset_v1.txt --version v1 \
  --buffer 512 --query top --top 10
```
 
**Difference query (two versions)**
```bash
./analyzer --file1 dataset_v1.txt --version1 v1 \
  --file2 dataset_v2.txt --version2 v2 \
  --buffer 512 --query diff --word error
```
 
## Output
 
Each run reports:
- Version name(s) involved in the query
- Query result
- Allocated buffer size (KB)
- Total execution time (seconds)
## Tech Stack
 
- **Language:** C++ (C++17)
- **Paradigm:** Object-Oriented Programming
- **Standard Library:** STL containers (`unordered_map`, `vector`, etc.)
## License
 
This project was developed as part of a CS253 coursework assignment.
