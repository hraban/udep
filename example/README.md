## Example

Sample usage of udep.

Usage:

    udep all

Look at the output in the `build` directory. Try deleting only the files
`build/a` and `build/all`, and run the above command again.

Example of parallel dependency execution:

    export PARALLEL=3
    udep all

Notice how the total wait time is only 3 seconds, not 2×3 = 6 seconds.

(N.B.: Parallel execution doesn't actually work properly with complicated rules. I'm leaving it in because I can, but it's broken as hell.)
