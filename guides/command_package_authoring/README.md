# Creating Sencha Cmd Packages

Sencha Cmd v3.1 includes the Sencha Package Manager. Packages have many uses even if you
do not intend to distribute the packages you create. This guide covers to process of
creating your own packages as well as distributing them. For information on using packages
please refer to [Sencha Cmd Packages](#/guide/command_packages).

{@img ../command/sencha-command-128.png}

## Generating Packages

Before we can generate a new package, we must first create a home for it: a
[Workspace](#/guide/command_workspace). For this example we will use Ext JS 4.2 for this
package, so we start with this command:

    sencha -sdk /path/to/ext-4.2.0 generate workspace /path/to/workspace

Now we navigate to the new workspace and generate the package:

    cd /path/to/workspace
    sencha -sdk ext generate package -type code foo

## Anatomy of a Package

The above steps will produce a starter package that contains many commonly used pieces.
You can delete many of these but it is important to keep the `".sencha"` folder intact.

The structure of a package generated by `sencha generate package` is as follows:

    packages/
        foo/                        # Top-level folder for the package
            .sencha/
                package/
                    sencha.cfg      # Sencha Cmd configuration for this package
                    build-impl.xml  # Generated build script for package
                    plugin.xml      # Sencha Cmd plugin for this package
                    codegen.json    # Data to support 3-way merge in code generator
            docs/                   # Documentation for the package
                screenshots/        # Screen shots for Sencha Market
            licenses/               # License agreement
            overrides/              # Folder for automatically activated overrides
            resources/              # Static resources (typically has images folder)
            sass/                   # Container for SASS code
                etc/                # General, non-component oriented SASS
                src/                # SASS rules and mixins named by component
                var/                # SASS variables named by component
            src/                    # Folder for normal JavaScript code
            build.xml               # Build script (called by `sencha package build`)
            package.json            # Package descriptor
            Readme.md               # High-level information about this package

While the above is quite extensive, the only parts that are required to be a package are
these: `"package.json"` and `".sencha/package/sencha.cfg"`.

The `"package.json"` file contains a description of the package. An example of this from
the `"ext-theme-neptune"` package:

    {
        "name": "ext-theme-neptune",
        "type": "theme",
        "creator": "Sencha",
        "summary": "Ext JS Neptune Theme",
        "detailedDescription": "The Neptune Theme provides a clean, modern style for Ext JS",
        "version": "4.2.1",
        "compatVersion": "4.2.0",
        "format": "1",
        "extend": "ext-theme-neutral"
    }

The `creator` property is something you will need to set as a package author. There is no
verification of this text, but it must match the name you assign to your local package
repository discussed later.

The `".sencha/package/sencha.cfg"` file is primarily important to the developer of the
package as it is used by Sencha Cmd during `sencha package build`. It is also used by
by user applications during their `sencha app build` process.

## Integrating Packages in Application Builds

The purpose of most packages is to (eventually) become integrated in an application. To
accomplish this, Sencha Cmd incorporates the various pieces of each required package in
to an application during the `sencha app build` process.

Precisely how this takes place depends on the `type` of the package.

### Package Type

Different types of packages play different roles. Sencha Cmd understands the following
types of packages:

  * **`code`** - An arbitrary package of code for use by applications or other packages.
  These packages are general purpose and are included when there is a `require` statement
  that selects them.
  * **`theme`** - A package to be used as an application's theme. Themes are special in that
  only one package of type `theme` is allowed to be "active" in a build. This theme is
  selected by the `app.theme` property  and it causes all other theme packages that may
  be in a `require` to be filtered out. Themes can also use `extend` to inherit SASS and
  resources from another theme package.
  * **`locale`** - A package containing localization strings or locale-specific code. These
  packages have a similar selection method to `theme` packages. Packages of this type add
  a property to their `"package.json"` called `"locale"`. This should be set to the name
  of the locale to which this package applies (e.g., `"he"` for Hebrew). The `app.locale`
  property, if set, causes any package of this type with a different value in its `locale`
  to be filtered out. This means you can have many different locale packages with the
  same `locale` value and they will all be included

### Source Code

The `"src"` folder is the place for classes such as custom components or other useful
code. This code is automatically included in the `classpath` for applications or other
packages to `require` and use.

This is where you would likely put most of your JavaScript code. Classes placed in this
folder should follow the [Compiler-Friendly Code Guidelines](#/guide/command_code).

### SASS

The `"sass"` folder contains three sub-folders designed to handle different aspects of
SASS compilation.

  * `"sass/etc"` - Code that does not relate directly to JavaScript classes
  * `"sass/var"` - Variable definitions (mirroring JavaScript class hierarchy)
  * `"sass/src"` - Mixins and rules (mirroring JavaScript class hierarchy)

The folders and files in `"sass/var"` and `"sass/ssrc"` are organized such that they are
mirror images of the JavaScript classes to which they apply. This correspondence allows
Sencha Cmd to include the files needed by the application. Files in these folders that
do not conform to this will never be included since the process proceeds by mapping the
JavaScript class hierarchy to the file system and not be scanning these folders.

The `package.sass.namespace` (in `".sencha/package/sencha.cfg"`) determines the top-level
namespace to which your styling applies. This defaults to `Ext` for packages. You will
most likely need to change this in order to associate your SASS with your classes inside
your package's namespace.

### Resources

The `"resources"` folder is where you place static resources needed by the package. When
applications consume the package, they will copy these resources into a sub-folder of their
own `"resources"` folder named by the package name. In this case, `"resources/foo"`. The
relative paths used by SASS will be automatically corrected if you use the provided
`theme-background-image` method.

### Overrides

The `"overrides"` folder is specifically intended for your package to provide mandatory
overrides, hence its name. This mechanism should be used cautiously because all code you
place in this folder will be automatically required in to any application that uses this
package.

The Ext JS Neptune Theme uses this mechanism to change certain default config properties
on various components. Locale packages use this to inject their own text on to the
prototypes of various components or to provide locale-specific logic for things such as
date formatting.

### Package Versions

Packages have a `version` property that describes its current version number. In
addition the current version (in the `version` property), packages can also indicate the
degree to which they are backward compatible using the `compatVersion` property.

These versions are used when resolving package requirements. Each release of a package
should have an updated version number. The meaning assigned to version numbers by Sencha
may help you:

    x.y.z.b

    x = Major release number (large, impacting changes and features)
    y = Minor release number (new functionality but few if any breaking changes)
    z = Patch release number (bug fix / maintenance release - goal of 100% compatible)
    b = Build number (assigned by build system)

The `version` property is typically the easiest to maintain. The `compatVersion`, however,
is an intentional statement about the degree to which users should be able to transparently
upgrade and not require code changes.

### Package Requirements

Packages can require other packages in the same way that applications can require packages.
To do this, you add to the `requires` array:

    {
        "name": "bar",
        "type": "code",
        "creator": "anonymous",
        "summary": "Short summary",
        "detailedDescription": "Long description of package",
        "version": "1.0.0",
        "compatVersion": "1.0.0",
        "format": "1",
        "local": true,
        "requires": [
            'ext-easy-button'
        ]
    }

**NOTE**: When using version restrictions as a package author, it is important to consider
that an application and all of the required packages will have to agree on a common version.
If you are too restrictive, this process can fail to find a mutually agreeable version for
all of the required packages.

### Package Inheritance

The notion of inheritance for packages is intended exclusively for Themes. These are
most important ways in which the inherited package contents are rolled into the derived
package:

  * The `"resources"` folders of inherited packages is copied in to the `"resources"`
  folder in the `"build"` output folder.
  * The `overrides` of base packages are automatically included in the build output of
  the derived package.

## Building The Package

To publish the package, you will need to build it using:

    sencha package build

This produces a `"build"` folder inside the package. This is needed by applications when
they are running in "dev mode" (without being compiled). It also produces `"foo.pkg"` file
in your workspace's `"build"` folder. This file is not placed in the package's `"build"`
folder because:

  * It is a ZIP file of your package folder.
  * It is not needed by users of the package.

The `"foo.pkg"` file is used to add the package to your local repository. See below.

**NOTE**: In Cmd v3.1.0, `sencha package build` may fail if you have SASS that depends
on the theme. To workaround this limitation, you can set the `skip.sass` and `skip.slice`
properties in `".sencha/package/sencha.cfg"`. The package will be usable in an application
because the theme will be included in `sencha app build`.

## Local Repository

The local repository was introduced in [Sencha Cmd Packages](#/guide/command_packages),
but there is more to know about it when you want distribute the packages you have created.

### Structure

The local repository generated by Sencha Cmd looks like this:

    .sencha/
        repo/
            sencha.cfg                  # Sencha Cmd configuration for the repo
            private-key.json            # Private key for repo

            remotes/                    # Storage for remote repositories
                remoteName/             # Name given at `sencha repo add`
                    catalog.json        # Last catalog from this remote

            trust/                      # Unused in this release
                <somename>.cert.json    # Copy of `cert.json` (a public key)

    pkgs/
        catalog.json                    # Catalog of all packages in this repo
        cert.json                       # Public key for this repo

        Foo/
            catalog.json                # Catalog for all versions of Foo package
            cert.json                   # Public key for creator of Foo package

            1.0/                        # Folder containing version 1.0
                Foo.pkg                 # Zip file of package
                package.json            # Extracted package descriptor
            1.1/
                ...

        Bar/
            ...

### Author Identity

When Sencha Cmd generates the default local repository, it does not require you to provide
any kind of identity. This is fine for its role as a cache, but as a package author you
need to put your name on the packages you publish. Before you can publish packages you
need to initialize your local repository with an identity:

    sencha package repo init -name "My Company" -email "support@mycompany.com"

After this step, your name and email address will be recorded in the local repository along
with a new [public/private key pair](http://en.wikipedia.org/wiki/Public-key_cryptography).

**NOTE**: The `name` argument must match the value of the `creator` property you set in
the `"package.json"`.

### Public/Private Keys

Your name, email and public key are stored in the `"pkgs/cert.json"` file. This file will
be automatically added to the packages you create to identify you as the package author.

Obviously given its name, your private key is not intended to be shared with others. It
is stored in your local repository in `".sencha/repo/private-key.json"`.

You might want to back up these two files as they will serve more important roles in
future releases.

### Content

The `"pkgs"` folder is where all the packages are stored. These may be packages you have
created or packages that you have downloaded.

When you add package `".pkg"` files, these will be copied in to the `"pkgs"` folder tree.

## Adding Packages

Once you have the `".pkg"` file from the build, and assuming that the `name` you have set
as your identity on your local repository matches the `creator` property defined in your
`"package.json"`, you can run this command:

    sencha package add foo.pkg

Sencha Cmd will produce a hash of the `".pkg"` file using your private key and add it to
the `".pkg"` file that it places in the local repository.

*NOTE*: In Cmd v3.1.0, the source file given the `package add` does not get this hash added
to it, only the copy added to the local repository.

Now that the package reside in the repository, other developers can `require` it if they
have added your repository as a remote.

The process to request that Sencha add your `".pkg"` to the Sencha Package Repository is
still being finalized, but you can check [Sencha Market](https://market.sencha.com/) for
updates on this.

## Hosting A Package Repository

The structure of the `"pkgs"` folder is the structure expected for a remote repository. All
that is required for others to `require` the packages you create is for them to add a
remote repository:

    sencha package repo add my-company http://my.company.com/packages

That is, assuming you have hosted your `"pkgs"` folder content at the above HTTP URL. There
is nothing required of that hosting beyond HTTP GET access so a static file server or CDN
will work.
