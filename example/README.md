## Example

Sample usage of udep.

Usage:

    udep all

Look at the output in the `build` directory. Try deleting only the file `a`, and
run the above command again.

Example of parallel dependency execution:

    export PARALLEL=3
    udep all

Notice how the total wait time is only 3 seconds, not 2×3 = 6 seconds.
