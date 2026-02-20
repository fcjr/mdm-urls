# mdm-urls

[![Validate](https://github.com/fcjr/mdm-urls/actions/workflows/validate.yml/badge.svg)](https://github.com/fcjr/mdm-urls/actions/workflows/validate.yml)

A public registry of known MDM (Mobile Device Management) server URL patterns.

Software vendors often need to determine whether a device is corporate-managed. Operating systems provide commands to check MDM enrollment status, which return MDM server URLs. This registry maps those URLs to known MDM vendors, enabling software to programmatically identify managed devices.

Inspired by [Normalize: Identifying Corporate Devices in Your Software](https://lgug2z.com/articles/normalize-identifying-corporate-devices-in-your-software/).

## Usage

Fetch the latest registry:

```
https://raw.githubusercontent.com/fcjr/mdm-urls/main/mdm_urls.json
```

Example (Python):

```python
import urllib.request, json

url = "https://raw.githubusercontent.com/fcjr/mdm-urls/main/mdm_urls.json"
registry = json.loads(urllib.request.urlopen(url).read())

mdm_server = "yourcompany.jamfcloud.com"  # from enrollment check

for vendor_id, entry in registry.items():
    if vendor_id.startswith("$") or vendor_id == "version":
        continue
    for pattern in entry["url_patterns"]:
        if pattern.startswith(".") and mdm_server.endswith(pattern):
            print(f"Matched: {entry['vendor']}")
        elif mdm_server == pattern:
            print(f"Matched: {entry['vendor']}")
```

## Checking MDM Enrollment

### macOS

```sh
profiles status -type enrollment
```

This returns the enrollment status and the MDM server URL if the device is managed.

### Windows

```cmd
dsregcmd /status
```

Look for the `MdmUrl` field in the output.

## Data Format

The registry is stored in [`mdm_urls.json`](mdm_urls.json) and validated against [`mdm_urls.schema.json`](mdm_urls.schema.json) ([JSON Schema 2020-12](https://json-schema.org/draft/2020-12/schema)).

The top-level object contains a `version` field and vendor entries keyed by a unique kebab-case ID (e.g. `microsoft-intune`):

```json
{
  "version": 1,
  "microsoft-intune": {
    "vendor": "Microsoft Intune",
    "url_patterns": ["enrollment.manage.microsoft.com", "manage.microsoft.com"],
    "info_url": "https://learn.microsoft.com/en-us/mem/intune/"
  }
}
```

| Field          | Type       | Description                                        |
| -------------- | ---------- | -------------------------------------------------- |
| `version`      | `integer`  | Schema version, incremented on structural changes  |
| `vendor`       | `string`   | Display name of the MDM vendor                     |
| `url_patterns` | `string[]` | Domain patterns found in MDM enrollment URLs       |
| `info_url`     | `string`   | Link to the vendor's product page or documentation |

Patterns may use a leading `.` to indicate a subdomain wildcard (e.g. `.jamfcloud.com` matches `yourcompany.jamfcloud.com`).

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for detailed instructions on adding a new vendor or updating an existing entry.

The short version: submit a PR adding your vendor to `mdm_urls.json` with evidence of the URL pattern. You can also [open an issue](https://github.com/fcjr/mdm-urls/issues/new/choose) to request an addition without a PR.

## License

[MIT](LICENSE)
