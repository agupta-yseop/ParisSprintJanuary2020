# January 28th : Beginner Workshop

(from 9.30AM to 6PM)

**Welcome!**

If you have not already downloaded them, please start now to download
[Visual Studio Build Tools for Windows](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools). Note that we do not need the full Visual Studio download (only the command line build tools).
Do not install it, we will do it together later.

## installing a Python dev environment

### git and github

- [git installation](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
(git bash for [Windows](https://git-scm.com/download/win)): git is a versioning system.
- [github](https://github.com) is a platform to work collaboratively on the source code of hosted Open Source projects such as scikit-learn and NumPy.

- Clone numpy with git:
  ```
  $ git clone https://github.com/numpy/numpy.git
  ```
- Fork scikit-learn on github then clone your fork with git:
  ```
  $ git clone https://github.com/<myuser>/scikit-learn
  ```
- Configure some aliases for the remote repositories:

  List existing aliases:
  ```
  $ git remote -v
  ```
  
  Add a new alias for your fork on github:
  ```
  $ git remote add upstream https://github.com/scikit-learn/scikit-learn.git
  ```
  
  Check that your new alias has been properly configured:
  ```
  $ git remote -v
  ```

### conda

#### Install miniconda

- [miniconda](https://docs.conda.io/en/latest/miniconda.html)
[installation](https://conda.io/projects/conda/en/latest/user-guide/install/index.html) (latest Python3 version)
- initialize the conda command in git bash
  - Windows: open "Git Bash" and type
  ```
  $ ./Miniconda3/Scripts/conda init bash
  ```
  - Linux:
  ```
  $ conda init bash
  ```
- then close your shell and start a new one to type:
  
  ```
  $ conda info
  ```
  or
  
  ```
  $ which conda
  ```
  to check that the conda command is in your PATH and useable from your shell.

#### conda environments

- create an environment (`sklworkshop`)
```
$ conda create --name sklworkshop
```
- activate and deactivate environments
```
$ conda activate sklworkshop
(sklworkshop)$ conda deactivate
```
- version, environment and package listing
```
$ conda --version
$ conda env list
$ conda list
```

### VS Code

Install VSCode following your
[system instructions](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform).

Launch VSCode and configure [Telemetry settings](https://code.visualstudio.com/docs/getstarted/telemetry).

Install the Python extension
- `Ctrl+Shift+X` open the extension manager
- search for the python extension: install

Open project folder for numpy: `Ctrl-k Ctrl-o` 

Open a Python file from the project: `setup.py`.

In order to work with VSCode in your Python environment
- `Ctrl+Shift+P` then "python select interpreter" and choose "sklworkshop"

Open project folder for scikit-learn: `Ctrl-k Ctrl-o`

Activate the "sklworkshop" Python interpreter for the scikit-learn project.

Switch between projects: `Ctrl-r`

Browse the code:
- by files `Ctrl-p`
- by symbols `Ctrl-t`

At some point VSCode will complain about not finding a linter: scikit-learn uses `flake8`
- Install `flake8` in your conda environment
```
$ conda activate sklworkshop
(sklworkshop)$ conda install flake8
```
- Select `flake8` as a linter in VSCode: `Ctrl-Shift-P` "select linter"

### Practical code navigation

Find example files that mention the word "importance" in different ways:
- VSCode: `Ctrl-p` "example importance" and open `plot_permutation_importance.py`
- Github: go to https://github.com/scikit-learn/scikit-learn in your browser, press `t` and type "example/importance"

Navigate to the RandomForestClassifier or Regressor from the `plot_permutation_importance.py` example:
- VSCode: ctrl-clicking on the class name
- github: clicking on the class name

Find the class KMeans in scikit-learn in two different ways:
- from the command line: using `git grep "class KMeans"` (-i case insensitive, note that without "class" you will find
all occurrences) 
- VSCode: `Ctrl-t` KMeans (disable the jedi extension if it doesn't work)

### Installing C/C++ compilers to be able to build native extensions

See instructions for your OS in the [installation guide](https://scikit-learn.org/stable/developers/advanced_installation.html#building-from-source).
- [Windows](https://scikit-learn.org/stable/developers/advanced_installation.html#windows)
- [macOS](https://scikit-learn.org/stable/developers/advanced_installation.html#macos)
- [Linux](https://scikit-learn.org/stable/developers/advanced_installation.html#linux)


## Building the master branch of numpy and scikit-learn

```
$ cd numpy
$ pip install -e .
$ cd ..
$ pip show numpy
$ conda install ipython
$ ipython
>>> import numpy as np
>>> print(np.__version__)
1.19.0.dev0+xxxxx
CTRL-D
```

Unlike numpy, scikit-learn does not list cython as a build dependency in a way understandable by pip yet. We need to install it manually first.
```
$ conda install cython
$ cd scikit-learn
$ pip install -e .
$ cd ..
$ pip show scikit-learn
$ ipython
>>> import sklearn
>>> print(sklearn.__version__)
0.23.dev0
CTRL-D
```

## Run a scikit-learn example
`plot_permutation_importance.py`

### from the command line
```
$ cd scikit-learn
$ ls examples
$ ls examples/inspection
$ python examples/inspection/plot_permutation_importance.py
```

Hint: at any moment use the `pwd` command to find where you are. `pwd` stands
for "Path to Working Directory". For instance here is a typical output one
would get on a Linux machine:

```
$ pwd
/home/ogrisel/code/scikit-learn
```

### from VScode
`ctrl-p` "plot permutation importance" then
`Ctrl-Shift-P` "Run Current File in Python Interactive Window"

## Run tests with pytest
```
$ conda install pytest
```

Each sklearn subpackage comes with a `tests` folder that gathers the test files related to it.

Use VSCode to open the source code for the RandomForestClassifier class.

- What is the path of this file?
- Can you find the folder that holds the tests for RandomForestClassifier in the VSCode file explorer?
- Can you find the test folder and list its content using the `ls` command?
- Locate the file `test_forest.py` in that folder.

Again use the `pwd` command if you are lost.

To run the test
```
$ pytest --verbose ./<path_to_test_folder>/test_forest.py
```

Edit the source code of RandomForestClassifier to change the predict method always return 0.
Rerun the test: what do you observe? 

alternatively you can use the `vlx` flags as follows:
```
$ pytest -vlx ./<path_to_test_folder>/test_forest.py
```
Find out about the meaning of those flags with
```
$ pytest --help
```

## Write a new test of your own

We will now do an exercise to add a new test function named `test_rf_regressor_prediction_range` in the `test_forest.py` file.

The goal of this new test will be to check that a `RandomForestRegressor`
never predicts numerical values outside of the range of values observed in
the training set.

The test will use a training set generated at random by [sampling from that
Normal distribution](https://docs.scipy.org/doc/numpy/reference/random/generated/numpy.random.mtrand.RandomState.normal.html?highlight=normal#numpy.random.mtrand.RandomState.normal).

For a test we can use a small dataset, for instance 100 samples and 10 features.

Here is a code template to get started:

```python
def test_rf_regressor_prediction_range():
    # Create a Random Number Generator with 42 as a fixed seed.
    rng = np.random.RandomState(42)

    # TODO: define the n_samples and n_features variables
    X_train = rng.normal(size=(n_samples, n_features))
    y_train = rng.normal(size=n_samples)

    rfr = RandomForestRegressor(random_state=42)
    rfr.fit(X_train, y_train)

    # TODO: np.min() and np.max() to measure the minimum and
    # maximum values of the training set target values y_train.

    # TODO: generate some random data X_test with the same number
    # of features.

    # TODO: compute the model predictions on the test data:
    # rfr.predict(X_test) and store the results in a variable y_preds.

    # TODO: check that all the values in y_preds lie between the minimum
    # and maximum values of y_train.
```

After each TODO, check that your code works as expected by running only
your test function with the following command:

```
$ pytest -vl -k test_rf_regressor_prediction_range \
     ./<path_to_test_folder>/test_forest.py
```

Bonus exercise: try to modify the code of scikit-learn to make your new test
fail by making it predict very large or very small values.

## Collaborative workflow via github (pull requests and code reviews)

What does branching mean?

https://learngitbranching.js.org/

https://learngitbranching.js.org/?NODEMO

- Create a new branch locally from your master
```
$ cd scikit-learn
$ git status
$ git branch my-awesome-branch
$ git status
$ git checkout my-awesome-branch
```
- Make your modifications and commit

- Check that the remote repository named `origin` points to your
  own github fork:
```
git remote --verbose
```

- Push the new branch to your fork:
```
$ git push origin my-awesome-branch
```
- From the github interface open a Pull Request (PR) to the `master`
  branch of your own scikit-learn fork. **Please do not open the PR to the
  scikit-learn/scikit-learn main repository!**

## Continuous Integration

- From the github interface open a PR to 
  - https://github.com/cmarmo/scikit-learn or
  - https://github.com/ogrisel/scikit-learn

**Again, please do not submit the PR to scikit-learn/scikit-learn!**


## Building the documentation with sphinx and sphinx gallery

Check the documentation [here](https://scikit-learn.org/stable/developers/contributing.html#documentation)
