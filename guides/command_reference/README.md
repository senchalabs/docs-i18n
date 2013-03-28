# sencha


## sencha ant

Invokes Apache Ant providing the `cmd.dir` property to access Sencha Cmd using
the following `taskdef`:

    <taskdef resource="com/sencha/ant/antlib.xml"
             classpath="${cmd.dir}/sencha.jar"/>


### Options
  * `--debug`, `-d` - enables ant debugging
  * `--file`, `-f` - Sets the ant file to execute
  * `--props`, `-p` - set properties for the ant script (name:value,...)
  * `--target`, `-t` - target(s) to execute within the ant script (comma separated)
  * `--verbose`, `-v` - enables ant verbose debugging

## sencha app

This category contains various commands for application management.


### Categories
  * `package` - Packages a Sencha Touch application for native app stores

### Commands
  * `build` - Executes the build process for an application
  * `refresh` - Updates the application metadata (aka "bootstrap") file
  * `resolve` - Generate a list of dependencies in the exact loading order for the given application.
  * `upgrade` - Upgrade the given application to the SDK at the current working directory

## sencha app build

This command builds the current application.

This command also supports ant invocation.

    sencha ant [production|testing|native|package] build

To tune the process, start by looking at the generated `build.xml` in your
application folder.


### Options
  * `--archive`, `-a` - The directory path where all previous builds were stored.
  * `--destination`, `-d` - The directory path to build this application to. Default: build
  * `--environment`, `-e` - The build environment, either 'testing', 'production', 'package' (Touch Specific), or 'native' (Touch Specific).
  * `--run`, `-r` - Enables automatically running builds with the native packager

## sencha app package



### Commands
  * `build` - Packages an app with the given configuration file
  * `generate` - Generates a Packager configuration JSON file
  * `run` - Packages and tries to run the application for the given configuration JSON file

## sencha app package build



### Options
  * `--path`, `-p` - the path to the configuration file

## sencha app package generate



### Options
  * `--path`, `-p` - the path to the configuration file

## sencha app package run



### Options
  * `--path`, `-p` - the path to the configuration file

## sencha app refresh

This command regenerates the metadata file containing "bootstrap" data for the
dynamic loader and class system.

This must be done any time a class is added, renamed or removed.


### Options
  * `--base-path`, `-b` - the base path to use to calculate relative path information. Defaults to index.html directory
  * `--metadata-file`, `-m` - the output filename for the js file containing the manifest metadata

## sencha app resolve

Generate a list of dependencies in the exact loading order for the current
application.

NOTE: the resolved paths are relative to the current application's HTML file.


### Options
  * `--output-file`, `-o` - The file path to write the results to in JSON format.
  * `--uri`, `-u` - The URI to the application\'s HTML document

## sencha app upgrade

This command upgrades the current application (based on current directory) to a
specified new framework.

    sencha app upgrade /path/to/sdk

NOTE: This will upgrade the framework used by the current application in the
current workspace. This will effect any other applications in this workspace
using the same framework (i.e., "ext" or "touch").


### Options
  * `--path`, `-pa` - The directory path to the framework to upgrade to

## sencha build

This command is used to process a legacy JSBuilder ("jsb") file.

DEPRECATED: This command is provided for backwards compatibility with previous
releases. It is highly recommended to migrate applications to the new `compile`
command and discontinue use of this command.


## sencha compile

This command category provides JavaScript compilation commands. The `compile`
category maintains compilation state across its sub-commands so using `and` to
connect sub-commands can provide significant time savings compared to making
repeated calls.


### Options
  * `--classpath`, `-cl` - adds folder(s) to the class path
  * `--debug`, `-deb` - enables the debug option for the js directive parser
  * `--deferred-overrides`, `-def` - enable / disable deferred override processing (use with optimize)
  * `--ignore`, `-ig` - excludes files with names containing the given substrings from the class path (comma separated)
  * `--options`, `-o` - sets options for the js directive parser (name:value,...)

### Commands
  * `concatenate` - produce a concatenated build
  * `exclude` - exclude files from the compilation set
  * `include` - include files into the compilation set
  * `intersect` - create a new save set by intersecting existing sets
  * `metadata` - produce class metadata
  * `page` - compiles a page
  * `restore` - alias for 'exclude +all and include -set=<set-name>'
  * `save` - remembers the currently enabled file state by a name
  * `show-ignored` - displays ignored files from the current classpath
  * `union` - alias for 'exclude +all and include ...'

## sencha compile concatenate

This command writes the current set to the specified output file.


### Options
  * `--append`, `-a` - Appends output to output file instead of overwriting output file
  * `--closure`, `-cl` - Compress generate file using Closure Compiler
  * `--compress`, `-co` - Compress generated file using default compressor (YUI)
  * `--output-file`, `-o` - the output file name
  * `--strip-comments`, `-str` - Strip comments from the generated file
  * `--uglify`, `-u` - Compress generate file using uglify-js
  * `--yui`, `-y` - Compress generated file using YUI Compressor

## sencha compile exclude

This command removes from the current set any files matching the criteria.


### Options
  * `--all`, `-a` - select all files in global cache (ignores other options)
  * `--class`, `-c` - Selects files according to the specified class names
  * `--file`, `-f` - Selects the specified file names (supports glob patterns)
  * `--namespace`, `-na` - Selects all files with class definitions in the given namespace(s)
  * `--not`, `-no` - inverts the matching criteria
  * `--recursive`, `-r` - Enable traversal of dependency relationships when selecting files
  * `--set`, `-s` - Selects files based on a previously named set created via a 'save' command (ignores other options)
  * `--tag`, `-t` - Selects all files with the specified '//@tag' values

## sencha compile include

This command adds the files matching the criteria to the current set.


### Options
  * `--all`, `-a` - select all files in global cache (ignores other options)
  * `--class`, `-c` - Selects files according to the specified class names
  * `--file`, `-f` - Selects the specified file names (supports glob patterns)
  * `--namespace`, `-na` - Selects all files with class definitions in the given namespace(s)
  * `--not`, `-no` - inverts the matching criteria
  * `--recursive`, `-r` - Enable traversal of dependency relationships when selecting files
  * `--set`, `-s` - Selects files based on a previously named set created via a 'save' command (ignores other options)
  * `--tag`, `-t` - Selects all files with the specified '//@tag' values

## sencha compile intersect

This command produces as in the current set the files that are contained in all
of the specified input sets. Alternatively, this command can include files that
are present in a present in a certain minimum number of sets.

This command only operates on saved sets (unlike most other set operations).


### Options
  * `--min-match`, `-m` - sets the minimum number of sets containing a file to cause a match (-1 = all)
  * `--name`, `-n` - the name of the resultant intersected set
  * `--sets`, `-s` - intersects the specified sets

## sencha compile metadata

This command generates various forms of metadata extracted from the current set
of files. This data can be exported in various formats (e.g., JSON or JSONP).


### Options

#### Data Type
Choose one of the following options

  * `--alias`, `-ali` - Generate class name to alias information
  * `--alternates`, `-alt` - Generate class alternate name information
  * `--definitions`, `-d` - Generate symbol information
  * `--filenames`, `-f` - Generate source file name information
  * `--loader-paths`, `-l` - Generate dynamic loader path information
  * `--manifest`, `-m` - Generate a class definition manifest file

#### Format
Choose one of the following options

  * `--json`, `-json` - Generate data in JSON format
  * `--jsonp`, `-jsonp` - Generate data in JSONP format using the given function
  * `--tpl`, `-t` - The line template for generating filenames as text (e.g. <script src="{0}"></script>)

#### Misc

  * `--append`, `-ap` - Appends output to output file instead of overwriting output file
  * `--base-path`, `-b` - Set the base path for relative path references
  * `--output-file`, `-o` - the output file name
  * `--separator`, `-s` - The delimiter character used to separate multiple templates

## sencha compile page

This command processes a markup file as input and generates an output file with
certain sections rewritten.

If the `-name` option is specified, the dependency graph of all required files
is saved as a file set with that name (see also the `save` command).

If the `-name` option is not specified, all required files are instead written
to the "all-classes.js" file.


### Options
  * `--append`, `-ap` - Appends output to output file instead of overwriting output file
  * `--classes-file`, `-cla` - the name of the js file containing the concatenated output
  * `--closure`, `-clo` - Compress generate file using Closure Compiler
  * `--compress`, `-co` - Compress generated file using default compressor (YUI)
  * `--input-file`, `-i` - the html page to process
  * `--name`, `-n` - sets a reference name for the page
  * `--output-page`, `-o` - the output html page
  * `--scripts`, `-sc` - inject the given script path into the generated markup ahead of the all classes file
  * `--strip-comments`, `-str` - Strip comments from the generated file
  * `--uglify`, `-u` - Compress generate file using uglify-js
  * `--yui`, `-y` - Compress generated file using YUI Compressor

## sencha compile restore

This command restores a saved set as the current set.


## sencha compile save

This command saves the current set with the specified name. A saved set can be
used as a criteria for set operations (e.g., `include`) via the `-set` option.
A saved set can also be restored as the current set via the `restore` command.


## sencha compile show-ignored

Displays a list of all files found in the `classpath` but matching an `-ignore`
criteria.


## sencha compile union

This command adds files matching the criteria to the current set. This is
similar to the `include` command except that this command first removes all
files from the current set. In other words, this command makes the current set
equal to only those files that match the criteria.


### Options
  * `--all`, `-a` - select all files in global cache (ignores other options)
  * `--class`, `-c` - Selects files according to the specified class names
  * `--file`, `-f` - Selects the specified file names (supports glob patterns)
  * `--namespace`, `-na` - Selects all files with class definitions in the given namespace(s)
  * `--not`, `-no` - inverts the matching criteria
  * `--recursive`, `-r` - Enable traversal of dependency relationships when selecting files
  * `--set`, `-s` - Selects files based on a previously named set created via a 'save' command (ignores other options)
  * `--tag`, `-t` - Selects all files with the specified '//@tag' values

## sencha config

This command can be used to either set configuration options singly or load a
configuration file to set multiple options.


### Options
  * `--file`, `-f` - Specifies a config file to load
  * `--prop`, `-p` - Specifies a configuration property and value to set

## sencha fs

This category provides commands for manipulating files.


### Commands
  * `concatenate` - Concatenate multiple files into one
  * `difference` - Generates deltas between two files in JSON format
  * `minify` - Minify a JavaScript file, currently support YUICompressor (default), Closure Compiler and UglifyJS

## sencha fs concatenate

This command combines multiple input files into a single output file.


### Options
  * `--from`, `-f` - List of files to concatenate, comma-separated
  * `--to`, `-t` - The destination file to write concatenated content

## sencha fs difference

This command produces a delta (or "patch") file between input files.


## sencha fs minify

This command produced minified files using various back-end compressors.


## sencha generate

This category contains code generators used to generate applications as well
as add new classes to the application.

### Commands
  * `app` - Generates a starter application
  * `controller` - Generates a Controller for the current application
  * `form` - Generates a Form for the current application (Touch Specific)
  * `model` - Generates a Model for the current application
  * `profile` - Generates a Profile for the current application (Touch Specific)
  * `theme` - Generates a theme page for slice operations (ExtJS Specific)
  * `view` - Generates a View for the current application (ExtJS Specific
  * `workspace` - Initializes a multi-app workspace

## sencha generate app

This command generates an empty application given a name and target folder.

The application can be extended using other `sencha generate` commands (e.g.,
`sencha generate model`).

Other application actions are provided in the `sencha app` category (e.g.,
`sencha app build`).


### Options
  * `--controller-name`, `-c` - The name of the default Controller
  * `--library`, `-l` - the pre-built library to use (core or all). Default: core
  * `--path`, `-pa` - The path for the generated application
  * `--theme-name`, `-t` - The name of the defualt Theme
  * `--view-name`, `-v` - The name of the defalut View

## sencha generate controller

This command generates a new Controller and adds it to the current application.


### Options
  * `--name`, `-n` - The name of the Controller to generate

## sencha generate form

This command generates a new form and adds it to the current application.


### Options
  * `--fields`, `-f` - Comma separated list of "name:type" field pairs
  * `--name`, `-n` - The name of the Form to generate
  * `--xtype`, `-x` - the xtype for the form.  Defaults to the lowercase form of the name.

## sencha generate model

This command generates a new Model class and adds it to the current application.


### Options
  * `--fields`, `-f` - Comma separated list of "name:type" field pairs
  * `--name`, `-n` - The name of the Model to generate

## sencha generate profile

This command generates a new Profile and adds it to the current application.

NOTE: Sencha Touch only.


### Options
  * `--name`, `-n` - The name of the Profile to generate

## sencha generate theme

This command generates a new Theme and adds it to the current application.


### Options
  * `--name`, `-n` - The name of the Theme to generate

## sencha generate view

This command generates a new View class and adds it to the current application.


### Options
  * `--name`, `-n` - The name of the View to generate

## sencha generate workspace

This command generates a workspace for managing shared code across pages or
applications.


### Options
  * `--path`, `-pa` - Sets the target path for the workspace

## sencha help

The `help` command generates help for other commands.

### Usage

sencha help <command>


## sencha js

This command loads and executes the specified JavaScript source file or files.

    sencha js file.js[,file2.js,...] [arg1 [arg2 ...] ]

#### Files

The first argument to this command is the file or files to execute. If there
are multiple files, separate them with commas. In addition to the command line
technique of specifying files, this command also recognizes the following
directive:

    //@require ../path/to/source.js

This form of `require` directive uses a relative path based on the file that
contains the directive. Any given file will only be executed once, in much the
same manner as the compiler.

#### Context

A primitive `console` object with the following methods is provided to the
JavaScript execution context:

 * `log`
 * `debug`
 * `info`
 * `warn`
 * `error`
 * `dir`
 * `trace`
 * `time` / `timeEnd`

Arguments beyond the first can be accessed in JavaScript with the global `$args`
array. The current directory can be accessed with `$curdir`.

The Sencha Cmd object can be accessed with `sencha`. This object has a `version`
property and a `dispatch` method.

    if (sencha.version.compareTo('3.0.0.210') < 0) {
        console.warn('Some message');
    } else {
        // dispatch any command provided by Cmd:
        sencha.dispatch([ 'app', 'build', $args[1] ]);
    }

Beyond the above, the executing JavaScript has full access to the JRE using
the `importPackage` and `importClass` methods.

For example:

    importPackage(java.io);

    var f = new File('somefile.txt');  // create a java.io.File object

For further details on the JavaScript engine provided by Java, consult the
Java Scripting guide:

http://docs.oracle.com/javase/6/docs/technotes/guides/scripting/programmer_guide/index.html


## sencha manifest

This category provides commands to manage application manifests.


### Commands
  * `create` - Generate a list of metadata for all classes found in the given directories

## sencha manifest create

This command generates a list of metadata for all classes.


### Options
  * `--output-path`, `-o` - The file path to write the results to in JSON format.
  * `--path`, `-p` - The directory path(s) that contains all classes

## sencha theme

This category contains commands for managing themes.


### Commands
  * `build` - Builds a custom theme from a given page

## sencha theme build

This command will build the specified theme's image sprites.


### Options
  * `--output-path`, `-o` - the destination path for the sliced images
  * `--page`, `-p` - the page to slice
  * `--theme-name`, `-t` - The name of the theme to build

## sencha which



### Options
  * `--output`, `-o` - Name of an output property file to write the location as a property
  * `--property`, `-p` - Name of the property to write to the output property file for the location
