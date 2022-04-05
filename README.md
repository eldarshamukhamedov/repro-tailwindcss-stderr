### What version of Tailwind CSS are you using?

3.0.23

### What build tool (or framework if it abstracts the build tool) are you using?

Tailwind CLI (`npx tailwind`)

### What version of Node.js are you using?

16.14.2

### What browser are you using?

N/A

### What operating system are you using?

Linux (GH Codespaces)

### Reproduction URL

https://github.com/eldarshamukhamedov/repro-tailwindcss-stderr/tree/main

Steps:

1. Install NPM dependencies `npm install`
2. Run `npm run build` to confirm there are no errors
3. Run `npm run build 2>stderr.txt` to pipe the build STDERR output to the `stderr.txt` file

Expected:

- A successful build does not output anything to STDERR (`stderr.txt` file is empty)

Actual:

- A successful build outputs "Done in XXms." to STDERR (`stderr.txt` file contains "Done in XXms.")

### Describe your issue

Some build systems ([RushJS](rushjs.io/)) treat _any_ output to STDERR as a failed build, regardless of the process exit code.

From the repro steps above, you can see that `tailwind` build succeeds, and the process terminates with a clean exit code, but `tailwind` outputs the "Done in XXms." string to STDERR, _not_ STDOUT.

Since any output to STDERR implies that something went wrong, some build systems (e.g. RushJS) will assume the build failed.

### Proposed solution

Output the build stats ("Done in XXms") to STDOUT, instead of STDERR. Continue to output actual build errors to STDERR.

### Workarounds considered

It is possible to write a bash script that reads `tailwind` STDERR output, checks that the only output is the build stats string, and then "swallows" the output. The script would need to carefully parse the overall STDERR output and continue to output _actual_ errors to STDERR. It's doable, but a bit tricky.
