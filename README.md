# Protocol Buffers for the Rest of Us

`protoletariat` has one goal: fixing the broken imports
for the Python code generated by `protoc`.

Fundamentally, `protoletariat` converts absolute imports to relative imports.

It's specific to protocol buffers however, and will only convert imports that
were generated from `.proto` files.

Here's an example of how to use the tool, called `protol`:

1. Create a few protobuf files

```protobuf
// thing1.proto
syntax = "proto3";

import "thing2.proto";

package things;

message Thing1 {
  Thing2 thing2 = 1;
}
```

```protobuf
// thing2.proto
syntax = "proto3";

package things;

message Thing2 {
  string data = 1;
}
```

2. Run `protoc` on those files

```sh
$ mkdir out
$ protoc --python_out=out --proto_path=directory/containing/protos thing1.proto thing2.proto
```

3. Run `protol` on the generated code

```sh
$ protol --create-init --overwrite -g out --proto-path=directory/containing/protos thing1.proto thing2.proto
```

The `out/thing1_pb2.py` file should show a diff containing at least these lines:

```patch
-import thing2_pb2 as thing2__pb2
-
+from . import thing2_pb2 as thing2__pb2
```

## Help

```
$ protol --help
Usage: protol [OPTIONS] PROTO_FILES...

  Rewrite protoc-generated imports for use by the proletariat.

Options:
  -g, --generated-python-dir DIRECTORY
                                  [required]
  -p, --proto-path DIRECTORY      [required]
  --overwrite / --no-overwrite    Overwrite all generated Python files with
                                  modified imports
  --create-init / --dont-create-init
                                  Create an __init__.py file under the
                                  `generated_python-dir` directory
  --help                          Show this message and exit.
```
