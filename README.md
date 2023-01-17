# js_docker_repro
Reproduction of js_rules docker transition osx issue

First - node_modules

```shell
$ bazel bulid //:node_modules
```

Both `tailwind` and `tailwind_layer` succeed:
```shell
$ bazel build //:tailwind
$ bazel build //:tailwind_layer
```

But `tailwind_image` does not...

```shell
$ bazel build //:tailwind_image
```

with `--verbose_failures` shows the command
```shell
/bin/bash -c 'source external/bazel_tools/tools/genrule/genrule-setup.sh; external/nodejs_linux_amd64/bin/nodejs/bin/node ./bazel-out/darwin-fastbuild-ST-4a519fd6d3e4/bin/node_modules/.aspect_rules_js/tailwindcss@3.2.4_postcss@8.4.20/node_modules/tailwindcss/lib/cli.js -o bazel-out/darwin-fastbuild-ST-4a519fd6d3e4/bin/tailwind.css -c ./tailwind.config.js')
# Configuration: fa3535a1fd9ac4c54360efc45194abb693bb7710dc2220b225f89f2f0737cc2b
# Execution platform: @local_config_platform//:host

Use --sandbox_debug to see verbose messages from the sandbox and retain the sandbox build root for debugging
/bin/bash: external/nodejs_linux_amd64/bin/nodejs/bin/node: cannot execute binary file
```

when building the `_layer` rule, `$(NODE_PATH)` evaluates to
`external/nodejs_darwin_amd64/bin/nodejs/bin/node`, whereas the `_image` rule
it evaluates to `external/nodejs_linux_amd64/bin/nodejs/bin/node`. This makes
sense, because I'm on a macbook, and `container_image` transitions to this platform.
