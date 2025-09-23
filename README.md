# qobs package index

The qobs package index contains the necessary metadata, build configurations and additional files for qobs to build external packages.

The existence of this index means that you don't have to PR the Qobs build configuration into every project you want to depend on.

[What is qobs?](https://github.com/qobs-build/qobs)

## How this works

This is how Qobs uses this repository when fetching external dependencies:

1. clone/extract dependency
2. does the dependency have a `Qobs.toml` file inside it? if yes, continue
3. otherwise, look inside [`qobs_index.json`](./qobs_index.json) (inside this repository)
4. if any URL matches the dependency URL, copy all files from the associated index directory into the dependency's directory
5. otherwise fail

This way, the index entries don't need to store all the library sources, instead they only need to store the `Qobs.toml` file and any other additional files needed for the build.

This comes at a disadvantage: Qobs has no way of knowing if the index entry is outdated or too new for the dependency. So you should try using newest versions of dependencies if possible.

## How to port a library

1. clone the project you want to port
2. create a `Qobs.toml` file, set `lib = true` in the `[target]` section
3. fill out the metadata and target fields so the library compiles. you can look inside this repository for examples
4. copy the files you created/modified into a new directory inside this repo (they'll get merged with the downloaded dependency when Qobs fetches it)
5. get the URL to the project and add it to the index:

   ```console
   $ qobs index add https://github.com/raysan5/raylib.git raylib
   #                ^^^^^^^^                         ^^^^ ^^^^^^
   #                |                                |         |
   #                |   make sure Git links end with `.git`    |
   #                |                                          |
   #                https is preferable                        |
   #                                                           |
   #                           specify the directory you created

   info: added dependency https://github.com/raysan5/raylib.git -> raylib
   ```

6. then, create a PR in this repository. if it gets merged, run `qobs index update` to update the global index (stored in `~/.cache/qobs/index` or `%LocalAppData%/qobs/index`)
