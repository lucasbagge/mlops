# MLOPS

##  Dependency manager with `Poetry`

When your Python project relies on external packages, you need to make sure you’re using the right version of each package. After an update, a package might not work as it did before the update. A dependency manager like Python Poetry helps you specify, install, and resolve external packages in your projects. This way, you can be sure that you always work with the right dependency version on every machine.

### Why?

A quate found on github:

> Packaging systems and dependency management in Python are rather convoluted and hard to understand for newcomers. Even for seasoned developers it might be cumbersome at times to create all files needed in a Python project: setup.py, requirements.txt, setup.cfg, MANIFEST.in and Pipfile. So I wanted a tool that would limit everything to a single configuration file to do: dependency management, packaging and publishing.It takes inspiration in tools that exist in other languages, like composer (PHP) or cargo (Rust). And, finally, I started poetry to bring another exhaustive dependency resolver to the Python community apart from Conda's.

### Create a New Poetry Project

You can create a new Poetry project by using the new command and a project name as an argument. In this tutorial, the project is called rp-poetry. Create the project, and then move into the newly created directory (for this we will use it as our mangement in the mlops work building):

```
% poetry new mlops
% cd mlops
```

By running poetry new mlops, you create a new folder named rp-poetry/. When you look inside the folder, you’ll see a structure:

```
mlops/
│
├── mlops/
│   └── __init__.py
│
├── tests/
│   ├── __init__.py
│   └── test_rp_poetry.py
│
├── README.md
└── pyproject.toml
```

### Inspect the Project Structure

The mlops/ subfolder itself isn’t very spectacular yet. Inside this directory, you’ll find an __init__.py file with your package’s version:

```
# mlops/__init__.py

__version__ = "0.1.0"
```

When you hop over to the tests/ folder and open test_rp_poetry.py, you’ll notice that mlops is already importable:

```
# tests/test_rp_poetry.py

from mlops import __version__

def test_version():
    assert __version__ == "0.1.0"
```

Poetry has also added a first test to the project. The test_version() function checks whether the __version__ variable of mlops/__init__.py contains the expected version. However, the __init__.py file isn’t the only place where you define the version of your package. The other location is the pyproject.toml file.

### Use the pyproject.toml File

One of the most important files for working with Poetry is the pyproject.toml file. This file isn’t an invention of Poetry. It’s a configuration file standard that was defined in PEP 518:

> This PEP specifies how Python software packages should specify what build dependencies they have in order to execute their chosen build system. As part of this specification, a new configuration file is introduced for software packages to use to specify their build dependencies (with the expectation that the same configuration file will be used for future configuration details)

The authors considered a few file formats for the “new configuration file” mentioned in the quote above. In the end, they decided on the TOML format, which stands for Tom’s Obvious Minimal Language. In their opinion, TOML is flexible enough, with better readability and less complexity than the other options, which are YAML, JSON, CFG, or INI. To see how TOML looks, open the pyproject.toml file:

```
[tool.poetry]
name = "mlops"
version = "0.1.0"
description = ""
authors = ["lucasbagge <lucasbagge@hotmail.com>"]

[tool.poetry.dependencies]
python = "^3.10"

[tool.poetry.dev-dependencies]
pytest = "^5.2"

[build-system]
requires = ["poetry-core>=1.0.0"]
build-backend = "poetry.core.masonry.api"

```

You can see four sections in the pyproject.toml file. These sections are called tables. They contain instructions that tools like Poetry recognize and use for dependency management or build routines.

If a table name is tool-specific, it must be prefixed with tool. By using such a subtable, you can add instructions for different tools in your project. In this case, there is only tool.poetry. But you might see examples like [tool.pytest.ini_options] for pytest in other projects.

In the [tool.poetry] subtable on line 3 above, you can store general information about your Poetry project. Your available keys are defined by Poetry. While some keys are optional, there are four that you must specify:

1) name: the name of your package
2) version: the version of your package, ideally following semantic versioning
3) description: a short description of your package
4) authors: a list of authors, in the format name <email>

When you start a new project with Poetry, this is the pyproject.toml file you start with. Over time, you’ll add configuration details about your package and the tools you’re using. As your Python project grows, your pyproject.toml file will grow with it. This is particularly true for the subtables [tool.poetry.dependencies] and [tool.poetry.dev-dependencies]. In the next section, you’ll find out how to expand these subtables.

### Use Poetry’s Virtual Environment

When you start a new Python project, it’s good practice to create a virtual environment. Otherwise, you may confuse different dependencies from different projects. Working with virtual environments is one of Poetry’s core features, and it’ll never interfere with your global Python installation.

However, Poetry doesn’t create a virtual environment right away when you start a project. You can confirm that Poetry hasn’t created a virtual environment by having Poetry list all virtual environments connected to the current project. If you haven’t already, cd into rp-poetry/ and then run a command:

```
$ poetry env list
```

For now, there shouldn’t be any output.

Poetry will create a virtual environment along the way when you run certain commands. If you want to have better control over the creation of a virtual environment, then you might decide to tell Poetry explicitly which Python version you want to use for it and go from there:

```
poetry env use python3
```
With this command, you’re using the same Python version that you used to install Poetry. Using python3 works when you have the Python executable in your PATH.
When you run env use, you’ll see a message:

```
Creating virtualenv mlops-uLQ2yaeZ-py3.10 in /Users/lucasbagge/Library/Caches/pypoetry/virtualenvs
Using virtualenv: /Users/lucasbagge/Library/Caches/pypoetry/virtualenvs/mlops-uLQ2yaeZ-py3.10
```

### Declare Your Dependencies

A key element of Poetry is its handling of your dependencies. Before you get the ball rolling, take a look at the two dependency tables in the pyproject.toml file:

```
[tool.poetry.dependencies]
python = "^3.10"

[tool.poetry.dev-dependencies]
pytest = "^5.2"
```

There are currently two dependencies declared for your project. One is Python itself. The other is pytest, a widely used testing framework. As you’ve seen before, your project contains a tests/ folder and a test_mlops.py file. With pytest as a dependency, Poetry can run your tests immediately after installation.

Make sure that you’re inside the mlops/ project folder and run a command:


```
$ poetry install
```

With the install command, Poetry checks your pyproject.toml file for dependencies then resolves and installs them. The resolving part is especially important when you have many dependencies that require different third-party packages with different versions of their own. Before installing any packages, Poetry figures out which version of a package fulfills the version constraints that other packages set as their requirements.

Besides pytest and its requirements, Poetry also installs the project itself. This way, you can import rp_poetry into your tests right away:

```
from mlops import __version__


def test_version():
    assert __version__ == '0.1.0'
```

With your project’s package installed, you can import rp_poetry into your tests and check for the __version__ string. With pytest installed, you can use the poetry run command to execute the tests:

```
poetry run pytest
```

### Install a Package With Poetry

You may have used pip before to install packages that aren’t part of the Python standard library. If you run pip install with the package name as an argument, pip looks for packages on the Python Package Index. You can use Poetry the same way.

If you want to add an external package like requests to your project, then you can run a command:

```
$ poetry add pandas
```

By running poetry add requests, you’re adding the latest version of the requests library to your project. You can use version constraints like pandas<=1.2.1  if you want to be more specific. When you don’t add any constraints, Poetry will always try to install the latest version of the package.

Sometimes there are packages that you only want to use in your development environment. With pytest, you spotted one of them already. Another common library includes a code formatter like Black, a documentation generator like Sphinx, and a static analysis tool like Pylint, Flake8, mypy, or coverage.py.

To explicitly tell Poetry that a package is a development dependency, you run poetry add with the --dev option. You can also use a shorthand -D option, which is the same as --dev:

```
$ poetry add black -D
```

You added pandas as a **project dependency** and black as a **development dependency**. Poetry has done a few things for you in the background. For one thing, it added your declared dependencies to the pyproject.toml file:

```
[tool.poetry.dependencies]
python = "^3.10"
pandas = "^1.4.3"

[tool.poetry.dev-dependencies]
pytest = "^5.2"
black = "^22.6.0"
```

Poetry added the pandas package as a project dependency to the tool.poetry.dependencies table, while it added black as a development dependency to tool.poetry.dev-dependencies.

Differentiating between project dependencies and development dependencies prevents installing requirements that a user doesn’t need to run the program. The development dependencies are only relevant for other developers of your package who want to run tests with pytest and make sure the code is properly formatted with black. When users install your package, they only install requests with it.

### Handle poetry.lock

When you run the poetry add command, Poetry automatically updates pyproject.toml and pins the resolved versions in the poetry.lock file. However, you don’t have to let Poetry do all the work. You can manually add dependencies to the pyproject.toml file and lock them afterward.

#### Pin Dependencies in poetry.lock

If you want to build a baysian model with Python, then you may want to use pymc to build your model. Add it to the tool.poetry.dependencies table in the pyproject.toml file:

```
[tool.poetry.dependencies]
python = "^3.10"
pandas = "^1.4.3"
pymc = "^2.3.8"
```

By adding pymc = "^2.3.8", you’re telling Poetry that it should install exactly this version. When you add a requirement to the pyproject.toml file, it’s not installed yet. As long as there’s no poetry.lock file present in your project, you can run poetry install after manually adding dependencies, because Poetry looks for a poetry.lock file first. If it doesn’t find one, Poetry resolves the dependencies listed in the pyproject.toml file.

As soon as a poetry.lock file is present, Poetry will rely on this file to install dependencies. Running only poetry install would trigger a warning that both files are out of sync and would produce an error because Poetry wouldn’t know of any beautifulsoup4 versions in the project yet.

To pin manually added dependencies from your pyproject.toml file to poetry.lock, you must first run the poetry lock command:

```
(base) lucasbagge@macbook-pro-tilhrende-lucas mlops % poetry lock
Updating dependencies
Resolving dependencies... (5.0s)

Writing lock file
```
By running `poetry lock`, Poetry processes all dependencies in your pyproject.toml file and locks them into the poetry.lock file. And Poetry doesn’t stop there. When you run poetry lock, Poetry also recursively traverses and locks all dependencies of your direct dependencies.

Now that you’ve pinned all dependencies, it’s time to install them so that you can use them in your project.

### Install Dependencies From poetry.lock

If you followed the steps from the previous section, then you’ve already installed pytest and black by using the poetry add command. You’ve also locked pymc, but you haven’t installed pandas yet. To verify that pandas isn’t installed yet, open the Python interpreter with the poetry run command:

```
poetry run python3
```

Executing poetry run pymc will open an interactive REPL session in Poetry’s environment. First, try to import requests. This should work flawlessly. Then try importing pandas. This should throw an error because pymc isn’t installed yet:

```
>>> import pandas
>>> import pymc
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ModuleNotFoundError: No module named 'pymc'
```

After locking dependencies with the poetry lock command, you have to run the poetry install command so that you can actually use them in your project:

### Distinguish Between pyproject.toml and poetry.lock


While the version requirement in the pyproject.toml file can be loose, Poetry locks the versions you’re actually using in the poetry.lock file. That’s why you should commit this file if you’re using Git. By providing a poetry.lock file in a Git repository, you ensure that all developers will use identical versions of required packages. When you come across a repository that contains a poetry.lock file, it’s a good idea to use Poetry for it.

With poetry.lock, you can make sure that you use exactly the versions the other developers are using. And if the other developers aren’t using Poetry, you can add it to an existing project that wasn’t set up with Poetry.