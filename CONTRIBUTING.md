# Contributing

Thanks for helping grow the MDM URL registry! Here's how to add a new vendor or update an existing entry.

## Adding a new vendor

### 1. Obtain the URL pattern

Run the MDM enrollment check on a managed device:

**macOS:**
```sh
profiles status -type enrollment
```

**Windows:**
```cmd
dsregcmd /status
```

Note the MDM server URL from the output.

### 2. Add the entry

Edit `mdm_urls.json` and add a new entry in alphabetical order by key:

```json
"my-vendor": {
  "vendor": "My Vendor",
  "url_patterns": [".myvendor.com"],
  "info_url": "https://myvendor.com/"
}
```

- **Key**: lowercase kebab-case (e.g. `my-vendor`)
- **vendor**: the official display name
- **url_patterns**: one or more domain patterns from the enrollment output. Use a leading `.` for subdomain wildcards (`.example.com` matches `anything.example.com`)
- **info_url**: link to the vendor's product page or documentation

### 3. Validate locally

```sh
python3 -m json.tool mdm_urls.json > /dev/null && echo "Valid JSON"
```

If you have `jsonschema` installed:

```sh
pip install jsonschema
python3 -c "
import json, jsonschema
schema = json.load(open('mdm_urls.schema.json'))
data = json.load(open('mdm_urls.json'))
jsonschema.validate(data, schema)
print('Schema validation passed')
"
```

### 4. Submit a PR

Open a pull request with your changes. The CI will automatically validate:

- JSON syntax
- Schema conformance
- Alphabetical key ordering
- No duplicate URL patterns across vendors

## Updating an existing entry

If a vendor has changed their MDM server URL or rebranded, submit a PR updating the relevant entry. Please explain the change in the PR description.

## What we won't accept

- Entries without evidence of a real MDM enrollment URL (we need to know how you obtained the pattern)
- Duplicate URL patterns already claimed by another vendor
- Vendors that are not publicly available MDM solutions
- Entries with broken or placeholder `info_url` links

## Review process

A maintainer will review your PR after CI passes. We may ask for additional evidence or clarification about the URL pattern. Please be patient — this is a volunteer-maintained project.
