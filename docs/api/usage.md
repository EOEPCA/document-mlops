# Usage

A part of the SharingHub server API is dedicated to SharingHub UI. Most routes are not supposed to be called directly,
with the exception of the STAC API that can be requested from your favorite client.

For more information, check-out our tutorials [Tutorials](../usage/tutorials.md) and [How-to guides](../usage/howto-guides.md).

## STAC API

There are multiple clients available to request STAC APIs, we will simply use `pystac` to parse the STAC API response to give an example.

You will need to install the dependencies, `pystac` and `requests`.

```bash
pip install requests pystac
```

Write in a file named "stac-download.py" the following content:

```python title="stac-download.py" linenums="1"
import time
from pathlib import Path

import requests
from pystac import Item, StacIO

stac_io = StacIO.default()
stac_io.headers = {"X-Gitlab-Token": "<your_access_token> or <your_personal_gitlab_token>"}
STAC_API_URL = "http://sharinghub.example.com/api/stac/collections/<collection-id>/items/<stac-id>"

item = Item.from_file(STAC_API_URL, stac_io=stac_io)
timestamp = int(time.time())
download_path = Path.cwd() / f"{Path(item.id).name}-{timestamp}"
print("Download path:", download_path)

for name, asset in item.get_assets().items():
    file_path = download_path / Path(name)
    file_path.parent.mkdir(parents=True, exist_ok=True)

    print("- Download: ", asset.href)
    response = requests.get(asset.href)
    response.raise_for_status()

    print("  Write", file_path)
    with file_path.open("wb") as f:
        f.write(response.content)

print("Done!")
```

You must set the `STAC_API_URL` domain to your SharingHub instance, set `<collection-id>` (example: `ai-model`),
and set `<stac-id>`. The `StacIO` part is necessary for authentication, complete it.

This script:

1. Load a `pystac.Item` from a SharingHub server. The item can represent an AI model, or a dataset.
2. Define a download path from the item STAC id and current timestamp.
3. Loop over each asset to download it.

Example output:

```bash
$ python stac-download.py
Download path: /home/eoepca/my-model-1717064398
- Download:  http://sharinghub.example.com/api/download/eoepca/my-model/repository/requirements.txt?ref=main
  Write /home/eoepca/my-model-1717064398/requirements.txt
- Download:  http://sharinghub.example.com/api/download/eoepca/my-model/repository/training.py?ref=main
  Write /home/eoepca/my-model-1717064398/training.py
Done!
```
