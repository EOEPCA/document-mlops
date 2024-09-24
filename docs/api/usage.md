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

```python title="Example stac-download.py" linenums="1"
import time
from pathlib import Path

import requests
from pystac import Item, StacIO

AUTH_HEADERS = {"X-Gitlab-Token": "<your_access_token> or <your_personal_gitlab_token>"}

stac_io = StacIO.default()
stac_io.headers = AUTH_HEADERS
STAC_API_URL = "https://sharinghub.develop.eoepca.org/api/stac/collections/dataset/items/sharinghub-test/wine-dataset"

# Get STAC item and download his assets

item = Item.from_file(STAC_API_URL, stac_io=stac_io)
item_assets = [
    (name, asset) for name, asset in item.get_assets().items() if asset.has_role("data")
]

timestamp = int(time.time())
download_path = Path.cwd() / f"{Path(item.id).name}-{timestamp}"
print("Download path:", download_path)

for name, asset in item_assets:
    file_path = download_path / Path(name)
    file_path.parent.mkdir(parents=True, exist_ok=True)

    print("- Download: ", asset.href)
    response = requests.get(asset.href, headers=AUTH_HEADERS)
    response.raise_for_status()

    print("  Write", file_path)
    with file_path.open("wb") as f:
        f.write(response.content)

print("Done!")
```

You must set the `X-Gitlab-Token` of `AUTH_HEADERS` with an access token of `read_api` scope.

This script:

1. Load a `pystac.Item` from a SharingHub. The item can represent an AI model, or a dataset.
2. Define a download path from the item STAC id and current timestamp.
3. Loop over each asset to download it.

Example output:

```bash
$ python stac-download.py
Download path: /home/eoepca/wine-dataset-1725616477
- Download:  https://sharinghub.develop.eoepca.org/api/download/sharinghub-test/wine-dataset/repository/wine-quality.csv?ref=main
  Write /home/eoepca/wine-dataset-1725616477/wine-quality.csv
Done!
```
