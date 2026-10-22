# Quick start: Google Colab (free GPU and notebooks)

[Google Colab](https://colab.research.google.com/) is a hosted Jupyter notebook service. For this hackathon its main use is **free compute, including a GPU, if your own laptop doesn't have one**. You don't need an account anywhere except a Google account.

> [!NOTE]
> Good tutorials already exist, so this page only covers what you need to get going. Start with Google's [Welcome to Colab](https://colab.research.google.com/notebooks/intro.ipynb) notebook and the [Colab FAQ](https://research.google.com/colaboratory/faq.html).

## Do you need a GPU at all?
Probably not for everything. The hackathon data is tabular (Parquet) and fits comfortably in memory, so pandas, scikit-learn, and most outlier/hotspot methods run fine on a normal laptop CPU. A GPU helps if you:
- train or fine-tune neural networks (for example a time-series autoencoder),
- run a small language model or embeddings locally instead of calling an API (otherwise use [OpenRouter](./openrouter_quickstart.md)),
- run heavy parallel computation.

## Option A: In the browser
1. Go to [colab.research.google.com](https://colab.research.google.com/) and sign in with a Google account.
2. **File > New notebook** (or open a notebook from GitHub: **File > Open notebook > GitHub**).
3. **Runtime > Change runtime type**, then choose a GPU (for example T4). Free GPUs are not guaranteed and what is offered changes over time.
4. Run a cell to check it works:

```python
!pandas
```

## Option B: From inside Visual Studio Code
Google offers an official [Colab extension for VS Code](https://marketplace.visualstudio.com/items?itemName=Google.colab). You keep your VS Code editor, Git and AI assistant, but the code runs on a Colab server (with GPU).

1. Install [VS Code](https://code.visualstudio.com/) and the [Colab extension](https://marketplace.visualstudio.com/items?itemName=Google.colab) (it builds on the [Jupyter extension](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.jupyter), which is installed with it).
2. Open or create an `.ipynb` file.
3. Click **Select Kernel > Colab** and sign in with your Google account when asked.
4. Choose **Auto Connect** (or pick a specific server type to get a GPU), then run your cells.
5. Handy commands (`Ctrl+Shift+P`): `Colab: Mount Google Drive to Server...`, `Colab: Remove Server`, `Colab: Sign Out`.

More: [extension user guide](https://github.com/googlecolab/colab-vscode/wiki/User-Guide) and the [project on GitHub](https://github.com/googlecolab/colab-vscode). The extension is relatively new; if something doesn't work, use the browser (Option A) as a fallback.

## Get the hackathon data into Colab
The data is published on Zenodo (link in the [main README](../README.md#data)). Colab's disk is temporary, so download the data at the start of your notebook:

```python
# Replace with the real Zenodo file URL once published
!wget -q "<ZENODO_FILE_URL>" -O data.zip
!unzip -q -o data.zip -d data
```

```python
import pandas as pd
measurements = pd.read_parquet("data/sensor_measurements.parquet")
```

Alternatively, mount Google Drive to keep files between sessions:

```python
from google.colab import drive
drive.mount("/content/drive")
```

## Tips and limitations
- **Sessions are temporary.** Runtimes disconnect when idle and have a maximum lifetime. Files in `/content` are lost afterwards. Save results to Drive or download them, and commit your code to GitHub.
- **Free resources aren't guaranteed.** GPU availability and usage limits vary; if you're out of quota, try later, use the CPU, or run on your own machine.
- **Packages:** many are pre-installed. Install extra ones with `!pip install -q <package>` at the top of the notebook so it can be re-run from scratch.
- **Reproducibility:** your submission must run from scratch locally (see [Deliverables](../README.md#deliverables)). Keep a `requirements.txt` and make sure your final solution doesn't depend on a Colab-only feature such as `google.colab` imports.
