# Run PyInstaller Script

A GitHub Action for running a Python script that runs PyInstaller. This may be a bit different than similar Actions; take note of the following Python script:

```python
# package.py

from PyInstaller.__main__ import run
from sys import platform
from os.path import join
from shutil import rmtree


APP_NAME = "Windows App"
MAIN_PY = "main.py"


def package() -> None:
    run([
        "--clean", "-y",
        "-n", APP_NAME,
        "-w", MAIN_PY
    ])
    if platform == "darwin":
        rmtree(join(".", "dist", APP_NAME))


if __name__ == "__main__":
    package()
```

Having a script like the above allows me to have all PyInstaller arguements and behavior in one file that works across Windows, Linux, and Mac. No need to worry about making sure PyInstaller is consistent across different shell scripts and workflow files.

Instead of running PyInstaller directly, this Action relies on the presence of a script similar to the above to call PyInstaller.

## Usage

Along with the packaging/build script, a requirements folder is required.

Here are the parameters:

```yaml
python-version:
  description: "Python version for environment to set up."
  default: "3.12"
  # Follow the pattern of the default if you want to use a different version. For example: "3.13"
architecture:
  description: "CPU architecture to target."
  # Valid options: "x64", "arm64"
requirements:
  description: "Path to requirements.txt file."
  default: "requirements.txt"
  # Make sure pyinstaller is listed in your requirements file. Otherwise, PyInstaller won't run.
script:
  description: "Path to Python script to run PyInstaller."
  # Example: "package.py"
  # The name of the script doesn't matter: the script just needs to call PyInstaller like the previous code example.
output-directory:
  description: "Path to PyInstaller output directory."
  default: "./dist"
  # This is the default PyInstaller output directory. If you use something else, do tell.
artifact-name:
  description: "Name for artifact to be produced."
  # If you are using this Action multiple times in one workflow file, make sure you don't have redundant names.
```

All parameters are required to have a value.

Since this Action just sets up a Python environment, runs a Python script, and expects a directory to exist, you *could* use this to do other Python things other than PyInstaller. I didn't think that far ahead, though.

## Limitations

1. Windows executables built by this Action are slightly larger than those built locally.
1. Linux executables built by this Action are MUCH larger (more than double the size) than those built locally.
1. Artifacts are double zipped in order to retain symlinks (very important for generated Mac executables).
