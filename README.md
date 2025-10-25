# lua-utility
I keep needing the same basic functions over and over in again in Lua projects..

When required, `math.randomseed(os.time())` is called.

## Standard Library Additions
- `string.trim(s)`: Trims whitespace on both ends of a string.
- `string.enquote(s)`: Adds double quotes around a string, and escapes any double quotes within it.
- `string.split(s, delimiter)`: Returns list of split substrings.
- `string.gsplit(s, delimiter)`: Split string as an iterator.

## Constants
- `OS`: `Windows` or `UNIX-like` based on detected path separator.
- `path_separator`: Forward or backward slash, depending on what was detected.
- `temp_directory`: System temporary directory, ending in a path separator.
- `commands`: Common shell commands that differ based on `OS`. All commands end with a space so that they can be concatenated directly.
  - `recursive_remove`: Remove a directory and everything in it.
  - `list`: Listing files in a flat output.
  - `which`: Detect presence of programs in path.
  - `move`: Move file(s).
  - `silence_output`: Prepended space instead of appended, can be concatenated to the end of a command to silence it.
  - `silence_errors`: Prepended space instead of appended, can be concatenated to the end of a command to silence errors.
- `version`: String, semantic version of utility.
- `path`: The path utility is in, but it only works when the script is called via an absolute path.

## Dependency Checking
- `utility.require()`: Lua's `require()`, but makes sure `utility.path` will be checked for `.lua` files.
- `utility.required_program(name)`: Throws error if the specified program is NOT in your system's path. (Caches results.)

## Running Programs
- `utility.capture_safe(command, get_status)`: Runs a command and returns its output. If `get_status`, returns the status number first.
- `utility.capture_unsafe(command)`: Runs a command and returns its output. Relies on `io.popen`, which isn't always available, and can hang indefinitely. Not recommended. (Falls back to `capture_safe` when `io.popen` isn't available.)
- `utility.capture` is an alias to `utility.capture_safe`. (DEPRECATED)

## Identifiers
- `utility.uuid()`: Returns a UUIDv4. (**Not cryptographically secure.**)
- `utility.tmp_file_name()`: Returns a file name that can be used in the system's temporary directory.

## File Handling
- `utility.make_safe_file_name(name)`: Overzealous string modification to make something that should be safe to use across systems.
- `utility.split_path_components(path)`: Returns path, file name, and extension. Extension can be `nil`, file name will contain the extension if present.
- `utility.open(name, mode, func)`: Opens a file and executes `func` on its file handle. Guarantees file handles are closed. If `func` not specified, returns a function to call with your function to run your function later (the file is opened immediately though).
- `utility.ls(path, func)`: Executes `func` on all file names within a path. If `func` not specified, returns a function to call with your function to run your function later (the list will be generated immediately though).
- `utility.path_exists(path)`: Checks if a path exists (can be opened as a file).
- `utility.file_exists(path)`: DEPRECATED. Same as `utility.path_exists`, mistakenly thought it only opened writable files.
- `utility.is_file(path)`: Returns `true` only for *writable* files, `false` for everything else. (**Note**: It is possible this can return `true` in rare cases where it shouldn't. See issue [#14](https://github.com/TangentFoxy/lua-utility/issues/14).)
- `utility.file_size(path)`: Returns file size in bytes. (**Note**: This does return values for directories and other special files..)

### File Locks
- `utility.get_lock(path)`: Blocks until a lock can be established. Cooperative locking. Returns a UUID that can be checked on release to catch *some* errors.
- `utility.release_lock(path, uuid)`: Releases a lock. If UUID is specified, will error if the lock was violated (but this can only check for errors with utility's locking mechanism).

## String Handling
- `utility.escape_quotes_and_escapes(s)`: Escapes backslashes and quotes in a string.

## Config File
If a `config.json` file is present next to utility, and the `dkjson` library is present, it can be loaded and saved through utility. So far, I have used this to store sensitive data in other repos, so `config.json` often should be included in `.gitignore` files or otherwise protected.
- `utility.get_config()`: Returns config table.
- `utility.save_config()`: Saves config table. (Any changes you make *must not replace* the config table, or saving will fail, as this state is kept by utility itself.)

## Table Handling
- `utility.deepcopy(table)`: Copies a table, handling any recursion present, and making sure metatables are copied as well.
- `utility.enumerate(list)`: Creates unique values using empty tables for each named item in the list. The named value for each enumeration is stored within it as `name` for debugging purposes. (Tables are used to discourage lazy interaction with the enumeration by avoiding integer keys. This also makes it more difficult to confuse enumeration values with something else.)
- `utility.inspect(table)`: Kikito's `inspect` library will be automatically loaded here if present.
- `utility.print_table(table)`: Using Kikito's `inspect`, or a fallback, will print a table's contents.

## Networking
- `curl_read(url, curl_options)`: Returns a string of the page obtained from `url` via `curl` command. `curl_options` is passed raw to `curl` if present.

---

I did accidentally change the programming interface of this library without updating the major version number, but this was before writing documentation, and certainly before anyone else may have used it, so I am not bumping the version number just for that. However, if I make the same mistake in the future, I will correct it with a patch to the existing major version number as a final patch before bumping the major version number.
