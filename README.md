# Fastlane in Bazel

This is an experiment I ran on some time off to see if Fastlane could be managed in Bazel using a hermetic toolchain.

Some observations:

* [ruby-build](https://github.com/rbenv/ruby-build) is installed by the ruleset, but its [dependencies](https://github.com/rbenv/ruby-build/wiki#suggested-build-environment) are not. They need to be present on the system prior to setup.
* MRI Ruby is the only environment tested by Fastlane. JRuby, etc. are "use at your own risk". Bazel does not make the process of building Ruby any faster.
* Fastlane requires to be run in a directory in the workspace containing a `Gemfile`. That same directory must also contain either a `fastlane/` directory, or be the `fastlane/` directory itself (meaning the `Gemfile` must be in here instead). Bazel by-default runs all executables inside their runfiles, so a [shim](./fastlane/fastlane.sh) to set the working directory to the workspace root is necessary. If you have multiple apps, you may need to customize this shim, or centralize the configuration.
* `rb_bundle_fetch` has some odd performance characteristics, particularly around how much of the `Gemfile` it downloads during a build. If your project has many Ruby applications all sharing one Gemfile, following a [single version policy](https://bazel.build/external/advanced#shadowing_dependencies_in_workspace), this may be of interest.
