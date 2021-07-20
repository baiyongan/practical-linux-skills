# Bash Pitfalls Case 60-3
## nounset

nounset (set -u) is the least bad of the three options, but has its fair share of gotchas too. If the goal is to catch typoed variable names, shellcheck does a better job at detecting those.

See What are the advantages and disadvantages of using set -u (or set -o nounset)?


