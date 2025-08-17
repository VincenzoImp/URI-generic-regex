# URI Generic Regex

This project provides a robust regex pattern that can parse various URI formats and extract their individual components including scheme, authority (userinfo, host, port), path, query, and fragment parts. The implementation supports IPv4, IPv6, and domain name formats for hosts.

## Features

- ✅ **RFC 3986 Compliant**: Follows the official URI specification
- ✅ **Multiple Host Types**: Supports IPv4, IPv6, and domain names
- ✅ **Complete Component Extraction**: Parses all URI parts (scheme, userinfo, host, port, path, query, fragment)
- ✅ **Flexible Pattern Matching**: Handles various URI schemes (HTTP, HTTPS, FTP, SSH, etc.)
- ✅ **Named Groups**: Uses descriptive group names for easy component access
- ✅ **TLD Validation**: Includes comprehensive list of valid top-level domains

## URI Components Explained

According to RFC 3986, a URI has the following structure:

```
scheme://[userinfo@]host[:port]/path[?query][#fragment]
```

### Components:
- **Scheme**: Protocol identifier (e.g., `https`, `ftp`, `ssh`)
- **Userinfo**: Optional authentication information (`username:password`)
- **Host**: Server identifier (domain, IPv4, or IPv6 address)
- **Port**: Optional port number
- **Path**: Resource path on the server
- **Query**: Optional query parameters
- **Fragment**: Optional fragment identifier

## Installation

Simply download the `URI_generic_regex.py` file and import it into your Python project:

```python
from URI_generic_regex import URI_GENERIC_REGEX
import re
```

## Usage

### Basic Usage

```python
import re
from URI_generic_regex import URI_GENERIC_REGEX

# Sample URI
uri = "https://user:pass@www.example.com:443/path/to/resource?query=value#section"

# Find and extract components
match = re.search(URI_GENERIC_REGEX, uri, re.VERBOSE)

if match:
    components = match.groupdict()
    print("Scheme:", components['scheme'])        # https
    print("Userinfo:", components['userinfo'])    # user:pass@
    print("Host:", components['host'])            # www.example.com
    print("Port:", components['port'])            # :443
    print("Path:", components['path'])            # /path/to/resource
    print("Query:", components['query'])          # ?query=value
    print("Fragment:", components['fragment'])    # #section
```

### Finding Multiple URIs in Text

```python
text = """
Visit our website at https://example.com or contact us via 
ftp://files.example.org:21/downloads. For secure access, 
use https://secure.example.com:8443/login?redirect=home#top
"""

matches = re.finditer(URI_GENERIC_REGEX, text, re.VERBOSE)

for match in matches:
    print(f"Found URI: {match.group(0)}")
    print(f"Position: {match.start()}-{match.end()}")
    print(f"Components: {match.groupdict()}")
    print("-" * 40)
```

### Supported URI Examples

The regex successfully parses various URI formats:

```python
uris = [
    "https://example.com",
    "http://www.example.org/path/to/file.html",
    "ftp://username:password@ftp.example.net:21/directory/",
    "https://example.com/search?q=python&sort=date#results",
    "http://192.168.1.1:8080/admin",
    "https://[2001:db8::1]/ipv6-test",
    "ssh://user@server.com:22/home/user/",
    "file:///usr/local/bin/script.sh"
]

for uri in uris:
    match = re.search(URI_GENERIC_REGEX, uri, re.VERBOSE)
    if match:
        print(f"✅ Parsed: {uri}")
    else:
        print(f"❌ Failed: {uri}")
```

## Supported Host Types

### 1. Domain Names
- **Format**: `subdomain.domain.tld`
- **Example**: `www.example.com`, `api.service.org`
- **Validation**: Uses comprehensive TLD list from IANA

### 2. IPv4 Addresses
- **Format**: `xxx.xxx.xxx.xxx`
- **Example**: `192.168.1.1`, `10.0.0.1`
- **Range**: 1-3 digits per octet

### 3. IPv6 Addresses
- **Format**: `[xxxx:xxxx:xxxx:xxxx:xxxx:xxxx:xxxx:xxxx]`
- **Example**: `[2001:db8::1]`, `[::1]`
- **Note**: Must be enclosed in square brackets

## Named Groups Reference

| Group Name | Description | Example |
|------------|-------------|---------|
| `scheme` | Protocol identifier | `https`, `ftp`, `ssh` |
| `userinfo` | Authentication info | `user:pass@` |
| `host` | Complete host part | `www.example.com` |
| `ipv6` | IPv6 address only | `2001:db8::1` |
| `ipv4` | IPv4 address only | `192.168.1.1` |
| `domain` | Domain name only | `www.example.com` |
| `port` | Port number | `:443`, `:8080` |
| `path` | Resource path | `/path/to/file` |
| `query` | Query parameters | `?key=value&foo=bar` |
| `fragment` | Fragment identifier | `#section` |

## Advanced Examples

### Extract Specific Components

```python
def extract_domain_and_port(uri):
    match = re.search(URI_GENERIC_REGEX, uri, re.VERBOSE)
    if match:
        groups = match.groupdict()
        domain = groups.get('domain') or groups.get('ipv4') or groups.get('ipv6')
        port = groups.get('port', '').lstrip(':') if groups.get('port') else None
        return domain, port
    return None, None

# Example usage
domain, port = extract_domain_and_port("https://api.example.com:8443/v1/users")
print(f"Domain: {domain}, Port: {port}")  # Domain: api.example.com, Port: 8443
```

### Validate URI Format

```python
def is_valid_uri(uri):
    return bool(re.match(URI_GENERIC_REGEX, uri, re.VERBOSE))

# Test URIs
test_uris = [
    "https://example.com",          # ✅ Valid
    "not-a-uri",                   # ❌ Invalid
    "ftp://files.example.org",     # ✅ Valid
    "://missing-scheme.com"        # ❌ Invalid
]

for uri in test_uris:
    status = "✅ Valid" if is_valid_uri(uri) else "❌ Invalid"
    print(f"{uri:<30} {status}")
```

## Limitations

- **IPv6 Simplified**: Currently supports basic IPv6 format (8 groups of 4 hex digits)
- **Percent Encoding**: Basic support for percent-encoded characters
- **Scheme Validation**: Accepts any valid scheme format, doesn't validate specific protocols
- **Port Range**: Doesn't validate port number ranges (0-65535)

## Contributing

Contributions are welcome! Please feel free to submit issues, feature requests, or pull requests.

### Areas for Improvement
- Enhanced IPv6 support (compressed notation, mixed notation)
- Stricter port number validation
- Extended percent-encoding support
- Additional URI scheme validations

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

## References

- [RFC 3986 - Uniform Resource Identifier (URI): Generic Syntax](https://www.rfc-editor.org/rfc/rfc3986)
- [IANA Top Level Domains](https://data.iana.org/TLD/tlds-alpha-by-domain.txt)

---

**Note**: This regex is designed for general URI parsing. For production applications, consider using specialized URI parsing libraries that provide more comprehensive validation and error handling.
