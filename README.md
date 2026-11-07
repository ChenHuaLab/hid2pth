# hid2pth

A small command-line tool that converts ABIF `.hid` files (capillary electrophoresis raw data) into PyTorch `.pth` tensor files.

It works in two modes:

1. **Single file**: convert one `.hid` file to a `.pth` file at a path you choose.
2. **Batch**: convert every `.hid` file in a folder to a `.pth` file with the same name (only the extension changes) in an output folder.

## Download

Download the latest `hid2pth` binary from the [Releases](../../releases) page, then make it executable:

```bash
chmod +x hid2pth
./hid2pth -h
```

Requirements:

- Linux, x86_64
- No Python or PyTorch installation needed; everything is bundled in the binary.
- glibc 2.38 or newer (for example Ubuntu 24.04 or newer, Debian 13 or newer). On older distributions such as Ubuntu 22.04 or Debian 12 the binary will not start and reports an error like `GLIBC_2.38 not found`. Check your version with `ldd --version`.

> Note: the first start can take a few seconds because the bundled libraries have to be unpacked. In batch mode this happens only once per run.

## Usage

### Single file

```bash
./hid2pth single input.hid output.pth
```

### Batch (whole folder)

```bash
./hid2pth batch input_dir output_dir
```

- Only `.hid` files located directly in `input_dir` are converted (subfolders are not searched; the extension match is case-insensitive).
- `output_dir` is created if it does not exist.
- A failure on one file does not stop the run. A summary is printed at the end.

### Exit codes

| Code | Meaning |
|------|---------|
| `0`  | Everything succeeded |
| `1`  | An input was missing, no `.hid` files were found, or at least one file failed to convert |
| `2`  | Invalid command-line arguments (e.g. a missing argument or an unknown option) |

### Help

```bash
./hid2pth -h
./hid2pth single -h
./hid2pth batch -h
```

## Output format

Each `.pth` file contains a single `torch.Tensor`:

- **Shape:** `(num_channels, num_scans)`
- **dtype:** `torch.float32`
- **Content:** the raw (unprocessed) signal channels of the file, i.e. ABIF `DATA` tags 1-4 and 105-199 whose length equals the number of scans (`SCAN1`). Channels are stacked in the order they appear in the file.

Load it in Python with:

```python
import torch

x = torch.load("sample.pth")
print(x.shape, x.dtype)  # e.g. torch.Size([5, 7000]) torch.float32
```

## Third-party software

The binary bundles the following open-source components, each under its own license:

- [PyTorch](https://pytorch.org/) (BSD-3-Clause style license)
- [Biopython](https://biopython.org/) (Biopython License / BSD-3-Clause)
- [Python](https://www.python.org/) and other libraries packaged by [PyInstaller](https://pyinstaller.org/)

## License

See [LICENSE](LICENSE).
