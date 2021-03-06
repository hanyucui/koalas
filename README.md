

# Koalas: Pandas APIs on Apache Spark <!-- omit in toc -->

The Koalas project makes data scientists more productive when interacting with big data, by augmenting Apache Spark's Python DataFrame API to be compatible with Pandas'.

Pandas is the de facto standard (single-node) dataframe implementation in Python, while Spark is the de facto standard for big data processing. With this package, data scientists can:
 - Be immediately productive with Spark, with no learning curve, if one is already familiar with Pandas.
 - Have a single codebase that works both with Pandas (tests, smaller datasets) and with Spark (distributed datasets).

[![Build Status](https://travis-ci.com/databricks/koalas.svg?token=Rzzgd1itxsPZRuhKGnhD&branch=master)](https://travis-ci.com/databricks/koalas)
[![Latest release](https://img.shields.io/pypi/v/koalas.svg)](https://pypi.org/project/koalas/)


## Table of Contents <!-- omit in toc -->
- [Dependencies](#dependencies)
- [Get Started](#get-started)
- [Documentation](#documentation)
- [Project Status](#project-status)
- [Development Guide](#development-guide)
  - [Environment Setup](#environment-setup)
  - [Running Tests](#running-tests)
  - [Building Documentation](#building-documentation)
  - [Contributions](#contributions)
  - [Coding Conventions](#coding-conventions)
  - [Release Instructions](#release-instructions)


## Dependencies

 - Spark 2.4. Some older versions of Spark may work too but they are not officially supported.
 - A recent version of Pandas. It is officially developed against 0.23+ but some other versions may work too.
 - Python 3.5+ if you want to use type hints in UDFs. Work is ongoing to also support Python 2.


## Get Started

Koalas is available at the Python package index:
```bash
pip install koalas
```

After installing the package, you can import the package:
```py
from databricks import koalas
```

Now you can turn a pandas DataFrame into a Spark DataFrame that is API-compliant with the former:
```py
import pandas as pd
pdf = pd.DataFrame({'x':range(3), 'y':['a','b','b'], 'z':['a','b','b']})

# Create a Spark DataFrame from pandas DataFrame
df = koalas.from_pandas(pdf)

# Rename the columns
df.columns = ['x', 'y', 'z1']

# Do some operations in place:
df['x2'] = df.x * df.x
```

## Documentation

Coming soon. Generating API docs for this project is the highest priority item we are working on.


## Project Status

This project is currently in beta and is rapidly evolving.
You should expect the following differences:

 - some functions may be missing (see the [Contributions](#Contributions) section)

 - some behaviour may be different, in particular in the treatment of nulls: Pandas uses
   Not a Number (NaN) special constants to indicate missing values, while Spark has a
   special flag on each value to indicate missing values. We would love to hear your use
   case if you find differences.
   
 - because Spark is lazy in nature, some operations like creating new columns only get 
   performed when Spark needs to print or write the dataframe.



## Development Guide

### Environment Setup

We recommend setting up a Conda environment for development:
```bash
conda create --name koalas-dev-env python=3.6
conda activate koalas-dev-env
conda install -c conda-forge --yes --file requirements-dev.txt
pip install -e .  # installs koalas from current checkout
```

Once setup, make sure you switch to `koalas-dev-env` before development:
```bash
conda activate koalas-dev-env
```

### Running Tests

To run all the tests, similar to our CI pipeline:
```bash
./dev/run-tests.sh
```

To run a specific test file:
```bash
python databricks/koalas/tests/test_dataframe.py
```

To run a specific test method:
```bash
python databricks/koalas/tests/test_dataframe.py DataFrameTest.test_Dataframe
```

### Building Documentation

To build documentation via Sphinx:

```bash
cd docs && make clean html
```

It generates HTMLs under `docs/_build/html` directory. Open `docs/_build/html/index.html` to check if documenation is built properly.


### Contributions

Please create a GitHub issue if your favorite function is not yet supported.

We also document all the functions that are not yet supported in the [missing directory](https://github.com/databricks/koalas/tree/master/databricks/koalas/missing). In most cases, it is very easy to add new functions by simply wrapping the existing Pandas or Spark functions. Pull requests welcome!

### Coding Conventions
We follow [PEP 8](https://www.python.org/dev/peps/pep-0008/) with one exception: lines can be up to 100 characters in length, not 79.

### Release Instructions
Only project maintainers can do the following.

Step 1. Make sure the build is green.

Step 2. Create a new release on GitHub. Tag it as the same version as the setup.py.
If the version is "0.1.0", tag the commit as "v0.1.0".

Step 3. Upload the package to PyPi:
```bash
rm -rf dist/databricks_koala*
python setup.py bdist_wheel
export package_version=$(python setup.py --version)
echo $package_version

python3 -m pip install --user --upgrade twine
python3 -m twine upload --repository-url https://test.pypi.org/legacy/ dist/koalas-$package_version-py3-none-any.whl
python3 -m twine upload --repository-url https://upload.pypi.org/legacy/ dist/koalas-$package_version-py3-none-any.whl
```
