# Node addon API and Build Notes

## Node-gyp

[gyp docs](https://gyp.gsrc.io/index.md)

[node-gyp](https://github.com/nodejs/node-gyp)

### Usage

```bash

node-gyp configure

node-gyp build

node-gyp rebuild

node-gyp clean

node-gyp clean --all

node-gyp clean --release

node-gyp clean --debug

```

### Examples

```json
// copy files to build directory
{
  "targets": [
    {
      "target_name": "hello",
      "cflags!": [ "-fno-exceptions" ],
      "cflags_cc!": [ "-fno-exceptions" ],
      "sources": [ "hello.cc" ],
      "include_dirs": [
        "<!@(node -p \"require('node-addon-api').include\")"
      ],
      "defines": [ "NAPI_DISABLE_CPP_EXCEPTIONS" ],
      "copies": [
        {
          "destination": "<(PRODUCT_DIR)",
          "files": [ "hello.d.ts" ]
        }
      ]

    }
  ]
}

```

## Node addon API

### Examples

```c++
#include <napi.h>

Napi::String Method(const Napi::CallbackInfo& info) {
  Napi::Env env = info.Env();
  return Napi::String::New(env, "world");
}

Napi::Object Init(Napi::Env env, Napi::Object exports) {
  exports.Set(Napi::String::New(env, "hello"),
              Napi::Function::New(env, Method));
  return exports;
}

NODE_API_MODULE(hello, Init)
```

```typescript
// hello.d.ts
export function hello(): void;

// hello.ts
import * as hello from './build/Release/hello'

console.log(hello.hello())
```
