# Create Custom Python Modules

Pedro Dousseau - February 20th, 2018

## Create a Package

A package is a folder with script modules. Inside of this folder must exist a \_\_init\_\_.py file (it can be empty) so that python can define the folder as a package.

The name of the folder will be the name of package.

## Insert on Python Packages Folder

To use the module in python it must have inside of one of its package folder. It is recommended to put custom modules on site-packages folder. Location:

- On mac: $HOME/Library/Python/3.6/lib/python/site-packages/
- On ubuntu: $HOME/.local/lib/python3.5/site-packages/

Make sure the site-packages exists. Run `mkdir -p path/to/folder/` if it doesn't.

Instead of placing the package on site-packages, you can use a symbolic link pointing to it instead. E.g.:

Move to package folder and run:

```
ln -s "$(pwd)" $HOME/.local/lib/python3.5/site-packages/name_for_the_package
```

## Check on Python

In python command line run:

```
# List all modules
help("modules")

# List modules coitaining anything with pac
help("modules pac")

# List package information
help("package_name")
```

