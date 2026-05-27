## Steps to reproduce
1. From the repository root, add a focused provider test that configures `Provider{Directories: []string{...}}` to model multiple watched directories.
2. Run `go test ./pkg/provider/file -run 'TestProvide(With|Without)WatchMultipleDirectories' -count=1`.
3. Observe the compiler output before implementing support for a directory list in the file provider configuration struct and logic.

## Observed
The test run fails at compile time because the provider did not expose a `Directories` field. The exact trace reported by Go was: `pkg/provider/file/file_test.go:218:3: unknown field Directories in struct literal of type Provider` (and the same error for the second test case). This demonstrates there was no way to configure multiple watched directories in the file provider.

## Expected
The file provider should accept a list of directories, load all dynamic configuration files across those directories, and watch them for updates when `watch` is enabled. A configuration using multiple directory paths should compile and run successfully, and updates in any configured directory should trigger a reload just like the existing single-directory behavior.
