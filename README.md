# SourceMap Upload Script

This document explains how to use either `upload-sourcemap.sh` or `upload_sourcemap.py` to upload an existing `sourcemap.zip` through OpenAPI.

## Prerequisites

- A valid `DF-API-KEY`
- A prepared `sourcemap.zip`
- For the shell version:
  - `bash`
  - `curl`
  - `jq`
  - `split`
  - `mktemp`
  - `wc`
- For the Python version:
  - `python3`

## Quick Start

Shell version:

```bash
sh ./upload-sourcemap.sh \
  --endpoint https://openapi.guance.com \
  --api-key "$DF_API_KEY" \
  --app-id gc_app_android_demo \
  --version 1.0.2 \
  --env daily \
  --file ./sourcemap.zip \
  --need-cover true
```

Python version:

```bash
python3 ./upload_sourcemap.py \
  --endpoint https://openapi.guance.com \
  --api-key "$DF_API_KEY" \
  --app-id gc_app_android_demo \
  --version 1.0.2 \
  --env daily \
  --file ./sourcemap.zip \
  --need-cover true
```

Expected output:

- `Init succeeded, uploadId=...`
- `Uploading part x/y`
- `Merge succeeded via /api/v1/rum_sourcemap/part_merge`
- `Upload complete`

## Arguments

Required:

- `--endpoint`: OpenAPI base URL
- `--api-key`: `DF-API-KEY`
- `--app-id`: RUM application ID
- `--file`: path to `sourcemap.zip`

Optional:

- `--version`: application version
- `--env`: deployment environment, for example `daily`, `gray`, or `prod`
- `--need-cover`: `true` or `false`, default is `false`
- `--chunk-size-mb`: multipart chunk size in MB, default is `10`, max is `10`
- `--merge-path`: custom merge endpoint if your deployment uses a different path
- `--cancel-path`: custom cancel endpoint if your deployment uses a different path

## Environment Variables

You can provide the same values through environment variables:

```bash
export DF_OPENAPI_ENDPOINT="https://openapi.guance.com"
export DF_API_KEY="your-api-key"
export DF_APP_ID="app_id_from_studio"
export DF_VERSION="1.0.2"
export DF_ENV="daily"
export DF_SOURCEMAP_FILE="./sourcemap.zip"
export DF_NEED_COVER="true"
```

Then run:

```bash
sh ./upload-sourcemap.sh
```

Or:

```bash
python3 ./upload_sourcemap.py
```

## Notes

- Both scripts upload an existing zip file only. They do not build or package sourcemaps.
- The zip file must already follow the required sourcemap packaging format.
- Both scripts use `DF-API-KEY` authentication only.
- Both scripts attempt to cancel the multipart upload automatically if the flow fails after init.
- The Python version uses only the Python standard library and does not require extra pip packages.

## Common Errors

- `uploadId is empty`
  - Usually means the same sourcemap already exists and overwrite is disabled.
  - Retry with `--need-cover true` if overwrite is intended.

- `Missing required command`
  - Install the missing shell tool and rerun the script.

- `Merge endpoint ... returned HTTP 404`
  - Your private deployment may use a custom merge path.
  - Retry with `--merge-path <your-path>`.
