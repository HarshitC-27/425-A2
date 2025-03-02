# DNS Resolver - Iterative and Recursive Lookup

This project implements a DNS resolver that supports both **iterative** and **recursive** DNS lookups. It is written in Python using the `dnspython` library.

## Features

- **Iterative DNS Resolution**: Resolves domain names by querying root servers, TLD servers, and authoritative servers in sequence.
- **Recursive DNS Resolution**: Delegates the resolution process to the system's default resolver.
- **Error Handling**: Gracefully handles errors such as timeouts, non-existent domains, and unreachable servers.

## Requirements

- Python 3.x
- `dnspython` library

## Installation

1. Install Python 3.x from [python.org](https://www.python.org/).
2. Install the `dnspython` library using pip:
   ```bash
   pip install dnspython
   ```

## Code Structure

#### 1. send_dns_query(server, domain)

- **Purpose**: Sends a DNS query to a specified server.
- **Input**:
  - `server`: The IP address of the DNS server to query.
  - `domain`: The domain name to resolve.
- **Completing the code**:  
   Send a DNS query to the given server using UDP and return the response, by using dns.query.udp() to send the query and return the response.
- **Output**: Returns the DNS response if successful, otherwise returns `None`.

#### 2. extract_next_nameservers(response)

- **Purpose**: Extracts and resolves the next set of nameservers from the DNS response.
- **Input**:
  - `response`: The DNS response object from a previous query.
- **Completing the code**: This function extracts NS (Name Server) records from the Authority Section of the response and resolves them to their IP addresses:
     - It extract NS records from response.authority. 
     - Resolve NS hostnames to IP addresses using dns.resolver.resolve().
     - Return the list of resolved IP addresses.
- **Output**: Returns a list of IP addresses for the next set of authoritative nameservers.

#### 3. iterative_dns_lookup(domain)

- **Purpose**: Performs iterative DNS resolution.
- **Input**:
  - `domain`: The domain name to resolve.
- **Completeing the code**:
  This function performs iterative DNS lookup by:
     - Starting from the root DNS servers.
     - Querying each level (ROOT → TLD → AUTH).
     - Extracting and resolving nameservers at each step.
     - Updated the stage as we move through Root → TLD → Authoritative.
- **Output**:
  - Prints the resolved IP address if found.

#### 4. recursive_dns_lookup(domain)

- **Purpose**: Performs recursive DNS resolution using the system's default resolver.
- **Input**:
  - `domain`: The domain name to resolve.
- **Completing the code**:
  - This function performs recursive DNS resolution using the system's default resolver.
   - Use dns.resolver.resolve() to fetch the result directly.
   - Before resolving the final IP address, the commented code can be used to print the intermediate NS hostnames.
- **Output**:
  - Prints the resolved IP address if found.

## Usage

The script accepts two command-line arguments:

1. **Mode** : `iterative` or `recursive`
2. **Domain** : The domain name to resolve

### Example Commands

1. Iterative DNS Lookup

```bash
python3 dns_resolver.py iterative google.com
```

2. Recursive DNS Lookup

```bash
python3 dns_resolver.py recursive google.com
```

### Example Output

#### Iterative Lookup

```
[Iterative DNS Lookup] Resolving google.com
[DEBUG] Querying ROOT server (198.41.0.4) - SUCCESS
[Iterative DNS Lookup] Resolving google.com
[DEBUG] Querying ROOT server (198.41.0.4) - SUCCESS
Extracted NS hostname: l.gtld-servers.net.
Extracted NS hostname: j.gtld-servers.net.
Extracted NS hostname: h.gtld-servers.net.
Extracted NS hostname: d.gtld-servers.net.
Extracted NS hostname: b.gtld-servers.net.
Extracted NS hostname: f.gtld-servers.net.
Extracted NS hostname: k.gtld-servers.net.
Extracted NS hostname: m.gtld-servers.net.
Extracted NS hostname: i.gtld-servers.net.
Extracted NS hostname: g.gtld-servers.net.
Extracted NS hostname: a.gtld-servers.net.
Extracted NS hostname: c.gtld-servers.net.
Extracted NS hostname: e.gtld-servers.net.
Resolved l.gtld-servers.net. to 192.41.162.30
Resolved j.gtld-servers.net. to 192.48.79.30
Resolved h.gtld-servers.net. to 192.54.112.30
Resolved d.gtld-servers.net. to 192.31.80.30
Resolved b.gtld-servers.net. to 192.33.14.30
Resolved f.gtld-servers.net. to 192.35.51.30
Resolved k.gtld-servers.net. to 192.52.178.30
Resolved m.gtld-servers.net. to 192.55.83.30
Resolved i.gtld-servers.net. to 192.43.172.30
Resolved g.gtld-servers.net. to 192.42.93.30
Resolved a.gtld-servers.net. to 192.5.6.30
Resolved c.gtld-servers.net. to 192.26.92.30
Resolved e.gtld-servers.net. to 192.12.94.30
[DEBUG] Querying TLD server (192.41.162.30) - SUCCESS
Extracted NS hostname: ns2.google.com.
Extracted NS hostname: ns1.google.com.
Extracted NS hostname: ns3.google.com.
Extracted NS hostname: ns4.google.com.
Resolved ns2.google.com. to 216.239.34.10
Resolved ns1.google.com. to 216.239.32.10
Resolved ns3.google.com. to 216.239.36.10
Resolved ns4.google.com. to 216.239.38.10
[DEBUG] Querying AUTH server (216.239.34.10) - SUCCESS
[SUCCESS] google.com -> 142.250.194.174
Time taken: 0.600 seconds
```

#### Recursive Lookup

```
[Recursive DNS Lookup] Resolving google.com
[SUCCESS] google.com -> 172.217.167.206
Time taken: 0.011 seconds
```

## Error Handling

The script handles the following errors:

### 1. Non existent domain

When a domain does not exist, the script will print an error message.

**Command**

```bash
python3 dns_resolver.py iterative nonexistentdomain12345.com
```

**Output**

```
[Iterative DNS Lookup] Resolving nonexistentdomain12345.com
[DEBUG] Querying ROOT server (198.41.0.4) - SUCCESS
Extracted NS hostname: l.gtld-servers.net.
Extracted NS hostname: j.gtld-servers.net.
Extracted NS hostname: h.gtld-servers.net.
Extracted NS hostname: d.gtld-servers.net.
Extracted NS hostname: b.gtld-servers.net.
Extracted NS hostname: f.gtld-servers.net.
Extracted NS hostname: k.gtld-servers.net.
Extracted NS hostname: m.gtld-servers.net.
Extracted NS hostname: i.gtld-servers.net.
Extracted NS hostname: g.gtld-servers.net.
Extracted NS hostname: a.gtld-servers.net.
Extracted NS hostname: c.gtld-servers.net.
Extracted NS hostname: e.gtld-servers.net.
Resolved l.gtld-servers.net. to 192.41.162.30
Resolved j.gtld-servers.net. to 192.48.79.30
Resolved h.gtld-servers.net. to 192.54.112.30
Resolved d.gtld-servers.net. to 192.31.80.30
Resolved b.gtld-servers.net. to 192.33.14.30
Resolved f.gtld-servers.net. to 192.35.51.30
Resolved k.gtld-servers.net. to 192.52.178.30
Resolved m.gtld-servers.net. to 192.55.83.30
Resolved i.gtld-servers.net. to 192.43.172.30
Resolved g.gtld-servers.net. to 192.42.93.30
Resolved a.gtld-servers.net. to 192.5.6.30
Resolved c.gtld-servers.net. to 192.26.92.30
Resolved e.gtld-servers.net. to 192.12.94.30
[DEBUG] Querying TLD server (192.41.162.30) - SUCCESS
[ERROR] Resolution failed.
Time taken: 0.476 seconds
```

### 2. Timeout

If a DNS query takes too long (e.g., due to an unreachable server), the script will timeout and move to the next server.

**Command**

```bash
python3 dns_resolver.py iterative unreachabledomain.com
```

**Output**

```
[Iterative DNS Lookup] Resolving unreachabledomain.com
[DEBUG] Querying ROOT server (198.41.0.4) - SUCCESS
Extracted NS hostname: l.gtld-servers.net.
Extracted NS hostname: j.gtld-servers.net.
Extracted NS hostname: h.gtld-servers.net.
Extracted NS hostname: d.gtld-servers.net.
Extracted NS hostname: b.gtld-servers.net.
Extracted NS hostname: f.gtld-servers.net.
Extracted NS hostname: k.gtld-servers.net.
Extracted NS hostname: m.gtld-servers.net.
Extracted NS hostname: i.gtld-servers.net.
Extracted NS hostname: g.gtld-servers.net.
Extracted NS hostname: a.gtld-servers.net.
Extracted NS hostname: c.gtld-servers.net.
Extracted NS hostname: e.gtld-servers.net.
Resolved l.gtld-servers.net. to 192.41.162.30
Resolved j.gtld-servers.net. to 192.48.79.30
Resolved h.gtld-servers.net. to 192.54.112.30
Resolved d.gtld-servers.net. to 192.31.80.30
Resolved b.gtld-servers.net. to 192.33.14.30
Resolved f.gtld-servers.net. to 192.35.51.30
Resolved k.gtld-servers.net. to 192.52.178.30
Resolved m.gtld-servers.net. to 192.55.83.30
Resolved i.gtld-servers.net. to 192.43.172.30
Resolved g.gtld-servers.net. to 192.42.93.30
Resolved a.gtld-servers.net. to 192.5.6.30
Resolved c.gtld-servers.net. to 192.26.92.30
Resolved e.gtld-servers.net. to 192.12.94.30
[DEBUG] Querying TLD server (192.41.162.30) - SUCCESS
[ERROR] Resolution failed.
Time taken: 0.452 seconds
```

### 3. Unreachable Server

If a server is unreachable, the script will try the next available server.

**Command**

```bash
python3 dns_resolver.py iterative example.com
```

**Output**

```
[Iterative DNS Lookup] Resolving example.com
[DEBUG] Querying ROOT server (198.41.0.4) - SUCCESS
Extracted NS hostname: l.gtld-servers.net.
Extracted NS hostname: j.gtld-servers.net.
Extracted NS hostname: h.gtld-servers.net.
Extracted NS hostname: d.gtld-servers.net.
Extracted NS hostname: b.gtld-servers.net.
Extracted NS hostname: f.gtld-servers.net.
Extracted NS hostname: k.gtld-servers.net.
Extracted NS hostname: m.gtld-servers.net.
Extracted NS hostname: i.gtld-servers.net.
Extracted NS hostname: g.gtld-servers.net.
Extracted NS hostname: a.gtld-servers.net.
Extracted NS hostname: c.gtld-servers.net.
Extracted NS hostname: e.gtld-servers.net.
Resolved l.gtld-servers.net. to 192.41.162.30
Resolved j.gtld-servers.net. to 192.48.79.30
Resolved h.gtld-servers.net. to 192.54.112.30
Resolved d.gtld-servers.net. to 192.31.80.30
Resolved b.gtld-servers.net. to 192.33.14.30
Resolved f.gtld-servers.net. to 192.35.51.30
Resolved k.gtld-servers.net. to 192.52.178.30
Resolved m.gtld-servers.net. to 192.55.83.30
Resolved i.gtld-servers.net. to 192.43.172.30
Resolved g.gtld-servers.net. to 192.42.93.30
Resolved a.gtld-servers.net. to 192.5.6.30
Resolved c.gtld-servers.net. to 192.26.92.30
Resolved e.gtld-servers.net. to 192.12.94.30
[DEBUG] Querying TLD server (192.41.162.30) - SUCCESS
Extracted NS hostname: a.iana-servers.net.
Extracted NS hostname: b.iana-servers.net.
Resolved a.iana-servers.net. to 199.43.135.53
Resolved b.iana-servers.net. to 199.43.133.53
[DEBUG] Querying AUTH server (199.43.135.53) - SUCCESS
[SUCCESS] example.com -> 23.192.228.80
Time taken: 0.818 seconds
```

## Contributors

- **Ananya Agarwal** (210130)
- **Harshit Chaudhary** (210421)
