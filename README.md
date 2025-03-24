# Autocomplete API Name Extraction

This project aims to extract all possible names from an autocomplete API running at [http://35.200.185.69:8000](http://35.200.185.69:8000). The solution explores the API's behavior, handles constraints, and efficiently extracts names from multiple API versions.

## Approach

### 1. **API Exploration**
We began by testing different API versions (`v1`, `v2`, `v3`) to understand their behavior and constraints. 

For each version, we identified:
- **Valid characters for queries**: Determining which characters were supported.
- **Maximum number of results returned**: Identifying how many results each query could return.
- **Rate limiting behavior**: Understanding the API’s limits on request frequency.

### 2. **Name Extraction Algorithm**
We implemented a **Breadth-First Search (BFS)** algorithm to efficiently explore the name space:

#### **Single Character Search:**
- Test all valid characters to identify which ones return results.
- This forms the basis for our search space.

#### **Multi-Character Search:**
- Use a queue to store prefixes to explore.
- For each prefix:
  - Make an API request
  - Add new names to our collection
  - If the maximum results are returned, add new prefixes to the queue

#### **Optimization:**
- **Track visited prefixes**: Avoid redundant API calls to prevent exploring the same prefix again.
- **Implement exponential backoff**: Handle rate limiting by waiting progressively longer before retrying.

### 3. **Version-Specific Adaptations**
- **v1**: Uses lowercase letters, returns up to 10 results.
- **v2**: Includes digits, returns up to 12 results.
- **v3**: Adds special characters (`.-+*`), returns up to 15 results, and requires URL encoding for special characters.

## Code Structure

- **`make_request()`**: Handles API requests with retry logic for rate limiting.
- **`extract_all_names()`**: Main function implementing the BFS algorithm to extract names.
- **Logging**: Used throughout the code to track progress, API responses, and any issues encountered.

## Findings

- The API has multiple versions with different characteristics.
- Rate limiting is implemented, requiring careful request management.
- Special characters in `v3` require URL encoding using `requests.utils.quote()`.
- The number of results returned increases with each version of the API.

## Challenges and Solutions

- **Rate Limiting**: We implemented **exponential backoff** with jitter to avoid overwhelming the API and prevent blocking.
- **Character Set Expansion**: Adapted the algorithm to account for new character sets (digits and special characters) in newer API versions.
- **URL Encoding**: We used `requests.utils.quote()` to properly encode special characters in queries for version `v3`.

## Results

### **v1:**
- **Total unique names found**: 16,128
- **Total API requests made**: 27,326
- **Time taken**: 44,053.87 seconds

### **v2:**
- **Total unique names found**: 12,722
- **Total API requests made**: 6,876
- **Time taken**: 12448.27 seconds

### **v3:**
- **Total unique names found**: 11,922
- **Total API requests made**: 3,480
- **Time taken**: 4,596.22 seconds

## Usage

1. **Install required packages:**
 requests==2.28.1
