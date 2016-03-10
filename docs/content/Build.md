Building Math.NET Spatial
=========================

If you do not want to use the official binaries, or if you like to modify,
debug or contribute, you can compile locally either using Visual Studio or
manually with the build scripts.

VisualStudio or Xamarin Studio
------------------------------

We clearly separate dependency management from the IDE, you should therefore
run `restore.cmd` or `restore.sh` once after every git checkout in order to restore
the dependencies exactly as defined. Otherwise Visual Studio and other IDEs
may fail to compile or provide correct IntelliSense.

Tests can be run with the usual integrated NUnit test runners or ReSharper.

MSBuild or XBuild
-----------------

Instead of a compatible IDE you can also build the solutions directly with
`msbuild`, or on Mono with `xbuild`. You may need to run `restore.cmd` or
`restore.sh` before, once after every git checkout in order to restore the dependencies.

    restore.cmd (or restore.sh)            # restore dependencies (once)
    msbuild MathNet.Spatial.sln            # only build for .Net 4 (main solution)
    xbuild MathNet.Spatial.sln             # build with Mono, e.g. on Linux or Mac

FAKE
----

The fully automated build including unit tests, documentation and api
reference, NuGet and Zip packages is using [FAKE](http://fsharp.github.io/FAKE/).

FAKE itself is not included in the repository but it will download and bootstrap
itself automatically when build.cmd is run the first time. Note that this step
is *not* required when using Visual Studio or `msbuild` directly.

    build.cmd    # normal build (.Net 4.0), run unit tests (.Net on Windows)
    ./build.sh   # normal build (.Net 4.0), run unit tests (Mono on Linux/Mac, .Net on Windows)
    
    build.cmd Build              # normal build (.Net 4.0)
    build.cmd Build incremental  # normal build, incremental (.Net 4.0)
    
    build.cmd Test        # normal build (.Net 4.0), run unit tests
    build.cmd Test quick  # normal build (.Net 4.0), run unit tests except long running ones
    
    build.cmd Clean  # cleanup build artifacts
    build.cmd Docs   # generate documentation (also DocsDev, DocsWatch)
    build.cmd Api    # generate api reference
    build.cmd Zip    # generate zip packages (.Net 4.0)
    build.cmd NuGet  # generate NuGet packages (.Net 4.0)
    
    build.cmd All    # build, test, docs, api reference (.Net 4.0)

If the build or tests fail claiming that FSharp.Core was not be found, see
[fsharp.org](http://fsharp.org/use/windows/) or install the
[Visual F# 3.0 Tools](http://go.microsoft.com/fwlink/?LinkId=261286) directly.

Creating a Release
------------------

While only maintainers can make official releases published on NuGet and
referred to from the website, you can use the same tools to make your own
releases for your own purposes.

Versioning is controlled by the release notes. Before building a new version,
first add a new release header and change notes on top of the `RELEASENOTES.md`
document in the root directory. The fake builds pick this up and propagate it
to the assembly info files automatically.

The build can then be launched by calling:

    build.sh All release    # full release build
    build.sh NuGet release  # if you only need NuGet packages
    build.sh Zip release    # if you only need Zip packages

The build script will print the current version as part of the the header banner,
which is also included in the release notes document in the build artifacts.
Example:

    //  __  __       _   _       _   _ ______ _______
    // |  \/  |     | | | |     | \ | |  ____|__   __|
    // | \  / | __ _| |_| |__   |  \| | |__     | |
    // | |\/| |/ _` | __| '_ \  | . ` |  __|    | |
    // | |  | | (_| | |_| | | |_| |\  | |____   | |
    // |_|  |_|\__,_|\__|_| |_(_)_| \_|______|  |_|
    // 
    // Math.NET Spatial - http://spatial.mathdotnet.com
    // Copyright (c) Math.NET - Open Source MIT/X11 License
    // 
    // Math.NET Spatial  v2.3.0-beta1

The artifacts are then ready in the `out/packages` directory.

Official Release Process (Maintainers only)
-------------------------------------------

*   Update `RELEASENOTES.md` file with relevant changes, attributed by contributor (if external). Set date.
*   Update `CONTRIBUTORS.md` file (via `git shortlog -sn`)

*   Build Release:

        build.sh All release

*   Commit and push release notes and (auto-updated) assembly info files with new "Release: v1.2.3" commit

*   Publish Release:

        build.sh PublishDocs
        build.sh PublishApi
        build.sh PublishTag
        build.sh PublishMirrors
        build.sh PublishNuGet

    In theory there is also a `Publish` target to do this in one step, unfortunately
    publishing to the NuGet gallery is quite unreliable.

*   Create new Codeplex and GitHub release, attach Zip files (to be automated)
*   Copy artifacts to [release archive](http://1drv.ms/1uZ5IF0) (to be automated)
*   Consider a tweet via [@MathDotNet](https://twitter.com/MathDotNet)
*   Consider a post to the [Google+ site](https://plus.google.com/112484567926928665204)
