# Trilobyte
_Powerful text pattern parsing made intuitive_

[`pip install trilobyte`](https://pypi.org/project/trilobyte/)

Key features:
* Variable assignment
* Smart algorithm
* Powerful expressions

Using an algorithm based on text _keypoints_, Trilobyte's implementation differs vastly from most other text searching engines available, such as those for Regex. A somewhat high-level overview of the algorithm is presented near the top of [`./trilobyte/keypoints/classes.py`](./trilobyte/keypoints/classes.py). This is copied below:
```python
# @dev
# If inputs have so far matched keypoint but keypoint is not yet completed,
#     `matched` = False, `completed` = False
# If previous inputs have matched keypoint, keypoint is complete, and next input does not match,
#     `matched` = True, `completed` = True
# If inputs have so far matched keypoint and keypoint is completed and cannot go on,
#     `matched` = True, `completed` = True
# If inputs have so far matched keypoint and keypoint is completed but can still go on,
#     `matched` = True, `completed` = False
# If previous inputs have matched keypoint, keypoint is yet to complete, and current input does not match,
#     `matched` = False, `completed` = True

# @dev
# @algorithm
# If !`matched` and !`completed` continue on current search branch with current keypoint
# If  `matched` and !`completed` continue on current search branch with current keypoint,
#                                while also forking a new search branch with next keypoint
# If !`matched` and `completed`  delete current search branch
# If  `matched` and `completed`  continue on current search branch with next keypoint

# @dev
# @algorithm
# Open new search branch with root keypoint at every new character in sequence

# @dev
# @algorithm
# When all branches have been computed, resolve conflicting (overlapping) branches,
# giving priority to branches discovered first (unless user specifies otherwise).
# Then, if user does not want recursive search, remove branches nested inside bigger branches.

# @update
# Kill branch early if already overlapped
```

## Docs

_Trilobyte is still under development; the following commands have mostly been implemented programmatically, but cannot be parsed from plain text yet._

```
\                               / Makes the trilo treat the following expression as normal text
~ ( num ) [ pat ]               / Take the negative of pat, optionally supplying max checking length as a
                                  number `num`
* [ text ]                        Ignore case

{ char1 - char2 }               / Command that detects any character between char1 and char2 on UNICODE
                                  (inclusive)
{ pat1 , pat2 , pat3 , ... }    / Command that detects any trilo between the list of alternatives (use `\,`
                                  to avoid compiler treating `,` as a delimiter)

@r ( $var / num ) [ pat ]       / Command that detects repeated patterns of pat (r for repeat),
                                  optionally specify a variable name `var` for the number of repetitions matched,
                                  or supplying a number `num` which fixes the number of repetitions
@d [ delim_pat ] [ pat ]        / Command that detects a list of pat delimited by delim_pat (d for delimited)
@a [ main_pat ] [ rep_pat ]     / Command that detects main_pat, followed by an optional repeated occurrence of
                                  rep_pat after (a for after)
@b [ rep_pat ] [ main_pat ]     / Command that detects main_pat, preceded by an optional repeated occurrence of
                                  rep_pat before (b for before)

%s                              / The space character
%t                              / The tab character
%n                              / The newline character
%r                              / The return character

%w                              / Any whitespace character, including newline
%m                              / Only spaces or tabs (m = s + t, also think of it mono - all on 1 line!)
%f                              / Only newline or return (f = n + r, also think of it as flush)
%U                              / Any uppercase character
%l                              / Any lowercase character
%a                              / Any alphabetical character
%d                              / Any numerical digit
%b                              / Any alphanumeric character (b for basic)

%v                              / Shorthand for any sequence of alphanumeric characters that doesn't
                                  start with a number (v for variable, as these are generally named in
                                  this convention)
%o                              / Shorthand for repetition of %w (o = r + w, also think of it as omni -
                                  everything!)
%e                              / Shorthand for repetition of %m (e = r + m, also think of it as exclusive)
%x                              / Shorthand for repetition of %f (x = r + f, also think of X as separation -
                                  that's what a sequence of %f is anyway)
%p                              / Shorthand for any comma-delimited sequence of %v (v for parameters, as
                                  these are generally formatted in this convention)

$var [ pat ]                    / Represents the expression that follows pat as a variable (use '' for pat
                                  in order to detect any possible expression)
```
