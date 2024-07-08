# ⬆️📦🚀 Upload Artifact Action

This action uploads any files as build artifacts in your GitHub Actions workflow.
It's a wrapper around [actions/upload-artifact](https://github.com/actions/upload-artifact)
with an additional `artifact` output, which is a JSON string with extended artifact metadata,
and default `path` input set to `${INPUT_NAME}.tar`.

To be paired with the [download-artifact](https://github.com/actions-rindeal/download-artifact) action,
which is again a wrapper around [actions/download-artifact](https://github.com/actions/download-artifact)
with an additional `artifact` input that accepts the extended artifact metadata JSON string.

## 🌟 How is this useful?

Both actions together allow you to easily perform cross-workflow-run or even cross-repository artifact transfers
with just a single variable being passed around.

## 📋 Usage

```yaml
- name: "Upload artifact"
  id: 'UPLOAD'
  uses: 'actions-rindeal/upload-artifact@v4'
  with:
    'name': 'my-artifact'
    # will upload `${GITHUB_WORKSPACE}/my-artifact.tar` file
```

Example of passing the metadata around:
```yaml
with:
  'artifact': ${{ toJSON(fromJSON(steps['UPLOAD'].outputs['ARTIFACT'])) }}
```

Example of accessing the metadata props:
```yaml
env:
  'ARTIFACT_NAME':  ${{ fromJSON(steps['UPLOAD'].outputs['ARTIFACT']).name }}
  'ARTIFACT_WF_ID': ${{ fromJSON(steps['UPLOAD'].outputs['ARTIFACT']).workflow_run.id }}
```

## 🔧 Inputs

| Name               | Description                                    | Default          |
|--------------------|------------------------------------------------|------------------|
| `name`             | Artifact name                                  | `'artifact'`     |
| `path`             | File, directory or wildcard pattern to upload  | `'${name}.tar'`  |
| `if-no-files-found`| Behavior if no files are found                 | `'warn'`         |
| `retention-days`   | Number of days before artifact expiry (0 for default retention) | `0` |
| `compression-level`| Zlib compression level for the artifact archive| `'6'`            |
| `overwrite`        | Whether to overwrite an existing artifact with the same name | `'false'` |

## 📤 Outputs

| Name           | Description                | Example                                                      |
|----------------|----------------------------|--------------------------------------------------------------|
| `artifact-id`  | The ID, usable in API      | `'1676293972'`                                               |
| `artifact-url` | The WebUI download URL     | `'https://github.com/org/repo/actions/runs/123/artifacts/456'`|
| `artifact`     | JSON with metadata         | See below                                                    |

## 📜 Extended Artifact Information

The `artifact` output provides a JSON object with detailed information about the uploaded artifact. Here's an example:

```json
{
  "id": 1676293972,
  "name": "my-artifact",
  "url": "https://api.github.com/repos/octo-org/octo-repo/actions/artifacts/1676293972",
  "archive_download_url": "https://api.github.com/repos/octo-org/octo-repo/actions/artifacts/1676293972/zip",
  "workflow_run": {
    "id": 9834162510,
    "repository_full_name": "octo-org/octo-repo",
    "repository_id": 1234567,
    "head_repository_id": 1234567,
    "head_branch": "master",
    "head_sha": "a9eb7e22d2c4809bf15c5beff6399ffe25f79f59"
  }
}
```

## 📚 Notes

For more details on artifact handling in GitHub Actions, refer to the [official documentation](https://docs.github.com/en/actions/using-workflows/storing-workflow-data-as-artifacts).
