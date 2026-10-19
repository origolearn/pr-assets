# pr-assets

Public screenshot storage for Origo pull requests, adapted from [PostHog/pr-assets](https://github.com/PostHog/pr-assets).

> Every upload is public, including images linked from private PRs. Upload only demo content without personal data, credentials, or private internal information.

## Upload an image

Authenticate `gh` with write access to `origolearn/pr-assets`. Python 3 generates the UUID.

```bash
set -euo pipefail
FILE=path/to/screenshot.png
KEY="$(date +%Y/%m)/$(python3 -c 'import uuid; print(uuid.uuid4())').${FILE##*.}"
SHA=$(base64 < "$FILE" | tr -d '\n' | gh api -X PUT "repos/origolearn/pr-assets/contents/$KEY" \
	-f message="add screenshot" -F content=@- --jq '.commit.sha')
echo "![screenshot](https://raw.githubusercontent.com/origolearn/pr-assets/$SHA/$KEY)"
```

Paste the resulting Markdown into the PR description. No local clone is needed.

## Conventions

- Store images at `YYYY/MM/<uuid>.<ext>` to avoid name collisions.
- Use the returned URL with its commit SHA. The image remains accessible through that commit if its path later changes or disappears from the default branch.
- Send base64 through stdin to avoid the shell argument size limit.
- If concurrent uploads return HTTP 409, rerun the upload once.
- Keep each image to a few megabytes. Host videos and large files elsewhere.
