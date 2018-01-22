Developer guide
###############


CORE (C/C++) built as library
	C/C++ API
Wrappers:
	Python
	Avizo
	Paraview
	Tomviz 

	

Essential Tools
***************

Directory structure
===================

Each CIL repository (public or private) is organized in subdirectories as follows:

::

  project_name/
    CMake/
    CMakeLists.txt
    License.txt
    Core/
    recipes/
    Wrappers/
        Python/
        Avizo/
        Paraview/
        Tomviz/

Git and GitHub
==============		
Code development is done via git on (public or private) repositories. The master branch should contain only the latest development stable code. Each new feature should be implemented on a separate branch. Merging of new features on the ``master`` branch is done via Pull Requests (PR).

We strongly encourage the use of issues, milestones and projects on GitHub to keep track of the development cycle.

Typical Workflow:

1) Use your branch for big and small changes
2) Pull from master frequently to reduce the amount of work needed when merging
3) Use pull requests (PR) to add features in the master, even if you merge them yourself. The best way is to branch from master, make the patch and issue a PR
4) Use issues for bugs, proposals and discussions in general. You can assign issues to someone to require their attention.
5) Close issues when possible. Use �closes #N� with N issue number in messages of commits when your commit fixes an issue. https://help.github.com/articles/closing-issues-using-keywords/
 
Some git commands
-----------------

.. code-block :: text
 
  git status
  git diff filename
  git add filename
  git commit
  git commit �m �message�
  git pull origin <branch>
  git push origin <branch>
  git rebase �i HEAD~4     # rebases interactively the last 3 commits: read the screen
  git checkout <branch>    # to change the actual local branch
  git checkout �b <branch> # to create and checkout a new local branch
  git branch -d <branch>   # to delete a local branch

 
**On conflict read what git says**. Modify the files that git says need attention. You will find in those files some lines like these:

.. code-block :: text

  <<<<<<< HEAD
    <link type="text/css" rel="stylesheet" media="all" href="style.css" />
  =======
    <!-- no style -->
  >>>>>>> master

**your branch** is between HEAD and =====, master is between ===== and master. Once removed that 
``git commit``


CMake
=====

Building of the CIL modules is done with CMake, and each module will contain the appropriate ``CMakeList.txt`` files. Although one could build the Core with CMake alone, we often use conda to build the Core library.

Anaconda
========

Refer to the next section.

Building with Conda
*******************

While building with conda, conda creates an environment for the purpose, copies all the relevant data, issues cmake and packages everything. It's pretty neat but it must be configured.

The conda build requires the presence of the so-called `conda recipe <https://conda.io/docs/user-guide/tasks/build-packages/recipe.html>`_
. A recipe lives in a directory where there are 2 or 3 files.

.. code-block :: text
  
  recipe/
    meta.yaml
    bld.bat
    build.sh

The ``meta.yaml`` file contains informations about the package that will be created, its dependency at run time and at build time. The ``bld.bat`` and ``build.sh`` are files invoked by ``conda`` during the build process and are dependent on the system: windows or unix.

In the following a ``meta.yaml`` for one of the ccpi packages. It should be self evident that one describes the package, its dependencies at runtime and build-time.

.. code-block :: yaml

  package:
    name: cil_regularizer
    version: {{ environ['CIL_VERSION'] }}

  build:
    preserve_egg_dir: False
    script_env: 
      - CIL_VERSION

  requirements:
    build:
      - boost ==1.64.0	
      - boost-cpp ==1.64.0 
      - python 3.5 # [py35]
      - python 2.7 # [py27]
      - cmake >=3.1
      - vc 14 # [win and py35] 
      - vc 9  # [win and py27]
      - numpy

    run:
      - boost ==1.64.0
      - vc 14 # [win and py35]
      - vc 9  # [win and py27]
      - python 3.5 # [py35]
      - python 2.7 # [py27]
      - numpy

  about:
    home: http://www.ccpi.ac.uk
    license: Apache v2.0
    summary: Regularizer package from CCPi

In the ``build.sh`` one specifies how to build the package. 

.. code-block :: text

  #!/usr/bin/env bash

  mkdir build
  cd build

  #configure
  BUILD_CONFIG=Release
  echo `pwd`
  cmake .. -G "Ninja" \
      -Wno-dev \
      -DCMAKE_BUILD_TYPE=$BUILD_CONFIG \
      -DCMAKE_PREFIX_PATH:PATH="${PREFIX}" \
      -DCMAKE_INSTALL_PREFIX:PATH="${PREFIX}" \
      -DCMAKE_INSTALL_RPATH:PATH="${PREFIX}/lib"

  # compile & install
  ninja install

There are a number of `environment variables <https://conda.io/docs/user-guide/tasks/build-packages/environment-variables.html>`_  that are set by conda, like ``${PREFIX}``. 



Building Core with conda

1) Clone the git repository git clone https://github.com/vais-ral/CCPi-FISTA_Reconstruction.git
2) Create a directory for the builds outside of the source directory
3) conda create �name cil �python=3.5 �numpy=1.12
4) module load python/anaconda (optional, depends on the actual machine installation)
5) source activate cil

Python Wrappers
***************

Python wrappers are our current primary endpoint. The distribution of the software is 
done via the `ccpi conda channel <https://anaconda.org/ccpi>`_
. 

This means that the Python wrappers are built using `conda <https://conda.io/docs/user-guide/tasks/build-packages/recipe.html>`_
. 
