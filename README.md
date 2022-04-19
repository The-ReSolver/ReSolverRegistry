The main motivation for having a registry is to allow for test-specific dependencies of custom packages created in this project. This means that each package is now in principle completely independent of the others (in practice this may not be the case depending on the implementation details for the code in the packages). Another benefit of this registry is the easier distribution of the packages for use either individually or as a group.

# Add Registry
To add this registry to your Julia install run the following command in the REPL
```
]registry add "git@github.com:The-ReSolver/ReSolverRegistry.git"
```
or use https protocol if you prefer for the repository url.

# Developing on this Registry
If you would like to develop packages that would go onto this registry then below is the desired workflow.

### Prerequisites
The LocalRegistry package needs to be installed in the global environment. The command ``run(`git --version`)`` needs to return no errors when in the Julia REPL. Finally run `export JULIA_PKG_USE_CLI_GIT=true` in the bash shell (or add it to the .bashrc or .bash_profile files). This might not be
strictly necessary for every environment but it should help with general robustness of the process (see
[here](https://github.com/GunnarFarneback/LocalRegistry.jl/blob/master/docs/ssh_keys.md#2-using-an-external-git-binary-with-julias-package-manager) or
[here](https://discourse.julialang.org/t/local-registry-rsa-key-problem/78188/4) for details)

### Adding a Package
Generate a package using the Julia REPL `]generate <package_name>`. Initialise a git repository and add .gitignore including the Manifest.toml in its contents.
Once this is completed you can do whatever work you want on this package and push it a remote repository on github (this might be a requirement but I'm unsure at this
time).

To add the package to the registry, first make sure the package environment is activated, and then run the following in the REPL
```
using LocalRegistry
register()
```
If there are multiple registries to choose from I believe it is possible to add the specific registry as a keyword argument. It is also possible to add the package
from outside the package environment but I haven't got the syntax correct on that method yet.

## Making Package Updates Available from the Registry
Once a package has been added to the registry it's state is effectively fixed in that registry. You may change the package, commit, and push the changes to your remote,
but the package as viewed from the registry will not change (if imported by another package that has it as a dependency it will not see those changes).

To properly integrate changes the package such that they can be imported from the registry the Project.toml file needs to be updated with a new version number.
```
name = "MyPackage"
uuid = ...
authors = ["<author> <author_email>"]
version = "0.1.1"
```
Once this version number has been change then run
```
using LocalRegistry
register()
```
Note that if the version number isn't changed then this command will exit with an error. With this command the registry is updated with a new version of the package
(stored in the Versions.toml file) and any changes will take effect in project which depend on it once the `]update` command has been run.

It may be possible to manually update the package in the registry by changing the commit hash in the Versions.toml to refer to a later state of the repository. This
could be dangerous, but there is some support for doing this [here](https://discourse.julialang.org/t/creating-a-registry/12094).
