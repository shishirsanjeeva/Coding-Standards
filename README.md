## Coding-Standards
# Filesystem structure of a Python project
<code>
Project_folder
  - package1
    -- test
      --- test_sample.py
    -- __init__.py
    -- sample.py
  - package2
    -- test
      --- test_sample.py
    -- __init__.py
    -- sample.py
  - README.rst
  - LICENSE
  - .gitignore
  - setup.py
  - requirements.txt
  - docs `
</code>

* #### The project vs the package
  - The python `project` is everything in the base directory. All files related to your python application will be in the project directory.
  - The `package`, on the other hand, is as subdirectory inside the project with the same name as the project itself. This package contains the source code of your application. The reason for having this package directory is to separate source code from other files. When we pip install our project, we will tell pip to only include the files contained in the package directory.
* #### Why __init__.py?
  - The package needs to contain at least an __init__.py file. This tells python that this directory is indeed a package.
  - When python loads this package, it automatically runs the __init__.py. Therefore, it can be useful to include initialisation steps for the package. In all my projects, I add at least two things to this __init__.py:
      - A variable called ROOT_DIR containing the absolute path to the location of the package. I have always found it useful for a package to know it’s absolute location. For instance, this can be used to load non-source files contained in the package. Relying on the current working directory is not a good idea. Anyone can modify the current working directory, and your application won’t always be launched from the same location.
      - The configuration of the logger for my package.

<code>from os.path import dirname,abspath
ROOT_DIR =dirname(abspath(__file__)
</code>

* #### .gitignore
  - A .gitignore file is a text file describing some files that should not be included in version control. There are many reasons for not wanting to source control certain files. For instance, the file could contain sensitive data such as passwords. You might also want to exclude large data files such as images.
* #### README
  - A readme should at least contain a simple description of your project and instructions for how to install and use the package.
* #### setup.py
  - A setup.py is a python file that contains information about the package you are installing.
* <code>import setuptools
  setuptools.setup(name='my_project', packages=['my_project'])
</code>
Here we are saying that the name of our package should be « my_project ». This name will be used in the package metadata stored by pip. The « packages » parameter takes the name of package directory to install. Previously, I said only the package part of our project structure would be installed, this « packages » parameter is why.

* #### Tracking requirements with requirements.txt
  - A good way of tracking dependencies is through a requirements.txt. This requirements.txt contains all the packages that your project needs and that are not part of the standard library. We will use this requirements.txt to make pip automatically download and install requirements for us.
  - Let’s make a very simple requirements.txt and add numpy to it (numpy is a package for scientific computing in python).
  - `numpy==1.18.2`
  - This file alone is not very useful, we need to tell pip about these requirements. For that, we must slightly update our setup.py.
  - <code>import setuptools
    with open('requirements.txt', 'r') as f:
      install_requires = f.read().splitlines()
    setuptools.setup(name='my_project', packages=['my_project'], install_requires=install_requires)
    </code>
    * As you can see, we have added the packages contained in the requirements.txt to our package setup. Therefore, when pip installs our package, it will search for that version of numpy. If it does not find it, it will download it for us.
   
      
#### Do:
* name the directory something related to your project. For example, if your project is named "Twisted", name the top-level directory for its source files `Twisted`. When you do releases, you should include a version number suffix: `Twisted-2.5`.

* create a directory `Twisted/bin` and put your executables there, if you have any. Don't give them a `.py` extension, even if they are Python source files. Don't put any code in them except an import of and call to a main function defined somewhere else in your projects. (Slight wrinkle: since on Windows, the interpreter is selected by the file extension, your Windows users actually do want the `.py` extension. So, when you package for Windows, you may want to add it. Unfortunately there's no easy distutils trick that I know of to automate this process. Considering that on POSIX the .py extension is a only a wart, whereas on Windows the lack is an actual bug, if your userbase includes Windows users, you may want to opt to just have the .py extension everywhere.)

* If your project is expressable as a single Python source file, then put it into the directory and name it something related to your project. For example, `Twisted/twisted.py`. If you need multiple source files, create a package instead (`Twisted/twisted/`, with an empty `Twisted/twisted/__init__.py`) and place your source files in it. For example, Twisted/twisted/internet.py.
put your unit tests in a sub-package of your package (note - this means that the single Python source file option above was a trick - you always need at least one other file for your unit tests). For example, `Twisted/twisted/test/`. Of course, make it a package with `Twisted/twisted/test/__init__.py`. Place tests in files like `Twisted/twisted/test/test_internet.py`.
add `Twisted/README` and `Twisted/setup.py` to explain and install your software, respectively, if you're feeling nice.

* `LICENSE` -This is arguably the most important part of your repository, aside from the source code itself. The full license text and copyright claims should exist in this file.

* 'setup.py` -If your module package is at the root of your repository, this should obviously be at the root as well.

* `requirements.txt` - A pip requirements file should be placed at the root of the repository. It should specify the dependencies required to contribute to the project: testing, building, and generating documentation. If your project has no development dependencies, or if you prefer setting up a development environment via setup.py, this file may be unnecessary.

* `docs` -Package reference documentation.

#### Don't:
* put your source in a directory called src or lib. This makes it hard to run without installing.
* put your tests outside of your Python package. This makes it hard to run the tests against an installed version.
* create a package that only has a `__init__.py` and then put all your code into `__init__.py`. Just make a module instead of a package, it's simpler.
* try to come up with magical hacks to make Python able to import your module or package without having the user add the directory containing it to their import path (either via PYTHONPATH or some other mechanism). You will not correctly handle all cases and users will get angry at you when your software doesn't work in their environment.
