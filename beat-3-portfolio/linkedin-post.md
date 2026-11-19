My first open source contribution was merged on November 16. I fixed a bug in cluv, a tool from Mila that researchers use to run jobs on computing clusters. Two maintainers approved the fix with no changes requested.

Here is what was wrong. When every job failed to start, the tool still reported success. A script that depended on it would keep going as if nothing was wrong. Now the tool reports the failure and the script can stop.

Before I wrote any code I proved the bug with a test that failed. I also built my own checks that review my work before a maintainer sees it. They caught three of my mistakes. One was a wrong sentence in my first comment. One was a plan that treated an open question as settled. One was a setup problem that would have sent other people's changes along with mine.

I used Claude Code as an assistant and said so in the pull request.

Next up is a second cluv pull request that is waiting for review. I have also claimed issues in two more projects.

Thank you to the cluv maintainers for the fast and kind review.

https://github.com/mila-iqia/cluv/pull/289

#opensource #python #AI
