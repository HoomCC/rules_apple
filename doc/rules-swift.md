# Build rules for Swift

<a name="swift_library"></a>
## swift_library

```python
swift_library(name, srcs, deps, module_name, defines, copts, resources,
structured_resources)
```

Produces a static library from Swift sources. The output is a pair of .a and
.swiftmodule files.

### Module names and imports

By default, `swift_library` modules get named after the full target label, where
each non-alphanumeric character is replaced with `"_"`. For example, if you need
to import a target at `//foo/bar:baz`, you would write

```swift
import foo_bar_baz
```

This default can be changed by specifying a `module_name` attribute, which will
replace the full module name.

### Objective-C interoperation

In general, all the rules of Objective-C/Swift interop apply in Bazel. Any
`swift_library` target can depend on `objc_library`, and vice-versa.

When importing Objective-C code into Swift, the same aforementioned module name
rules apply. If there's an `objc_library` target `//a/b:c` it can be imported
into Swift as:

```swift
import a_b_c
```

When importing Swift code into Objective-C, you will use the generated header.
For example, a `swift_library` target `//a/b:c` will have a header that can be
imported by Objective-C code as:

```c
#import "a/b/c-Swift.h"
```

### Whole Module Optimization

WMO can be enabled for an individual target by placing the compiler flag into
the `copts` attribute:

```python
swift_library(
  name = "foo",
  copts = ["-whole-module-optimization"]
)
```

It can also be enabled for all targets in the build using the Bazel flag
`--swiftcopt=-whole-module-optimization`.

### Toolchains

`swift_library` has a limited support for custom toolchains installed
*outside of Xcode*. This is useful for testing against Swift snapshots. To
specify such toolchain, use its location:

```shell
bazel build //target:app
    --define xcode_toolchain_path=/Library/Developer/Toolchains/swift-4.0-DEVELOPMENT-SNAPSHOT.xctoolchain/
```
---

<table class="table table-condensed table-bordered table-params">
  <colgroup>
    <col class="col-param" />
    <col class="param-description" />
  </colgroup>
  <thead>
    <tr>
      <th colspan="2">Attributes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>name</code></td>
      <td>
        <p><code><a href="https://bazel.build/versions/master/docs/build-ref.html#name">Name</a>, required</code></p>
        <p>A unique name for the target.</p>
      </td>
    </tr>
    <tr>
      <td><code>srcs</code></td>
      <td>
        <p><code>List of <a href="https://bazel.build/versions/master/docs/build-ref.html#labels">labels</a>; required</code></p>
        <p>Sources to compile into this library. Only <code>*.swift</code>
        is allowed.</p>
      </td>
    </tr>
    <tr>
      <td><code>deps</code></td>
      <td>
        <p><code>List of <a href="https://bazel.build/versions/master/docs/build-ref.html#labels">labels</a>; optional</code></p>
        <p>A list of Swift or Objective-C libraries to link together.</p>
      </td>
    </tr>
    <tr>
      <td><code>module_name</code></td>
      <td>
        <p><code>String; optional</code></p>
        <p>Sets the Swift module name for this target. By default
        the module name is the target path with all special symbols replaced
        by <code>_</code>, e.g. <code>//foo/baz:bar</code> can be imported as
        <code>foo_baz_bar</code>.</p>
      </td>
    </tr>
    <tr>
      <td><code>defines</code></td>
      <td>
        <p><code>List of strings; optional</code></p>
        <p>Values to be passed with <code>-D</code> flag to the compiler for
        this target and all swift_library dependents of this target.</p>
      </td>
    </tr>
    <tr>
      <td><code>copts</code></td>
      <td>
        <p><code>List of strings; optional</code></p>
        <p>Additional compiler flags. Passed to the compile actions of this
        target only.</p>
      </td>
    </tr>
    <tr>
      <td><code>resources</code></td>
      <td>
        <p><code>List of <a href="https://bazel.build/versions/master/docs/build-ref.html#labels">labels</a>; optional</code></p>
        <p>Files to include in the final application bundle. Based on their
        extensions, they will be processed or compiled as needed (.xib via
        ibtool, .xcassets via actool, etc.).</p>
        <p>These files are placed in the root of the bundle (e.g.
        <code>foo.app/...</code>) in most cases. However, if they appear to be
        localized (i.e. are contained in a directory called
        <code>*.lproj</code>), they will be placed in a directory of the same
        name in the app bundle.</p>
      </td>
    </tr>
    <tr>
      <td><code>structured_resources</code></td>
      <td>
        <p><code>List of <a href="https://bazel.build/versions/master/docs/build-ref.html#labels">labels</a>; optional</code></p>
        <p>Files to include in the final application bundle. They are not
        processed or compiled in any way besides the processing done by the
        rules that actually generate them.</p>
        <p>Unlike <code>resources</code> these files are placed in the bundle
        root in the same structure passed to this argument, so
        <code>["res/foo.png"]</code> will end up in
        <code>foo.app/res/foo.png</code>.</p>
      </td>
    </tr>
  </tbody>
</table>
