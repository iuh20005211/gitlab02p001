Skip to content
git
/
git

Type / to search

Code
Pull requests
156
Actions
Security
21
Insights
Please verify your email address to access all of GitHub’s features.
An email containing verification instructions was sent to lethithanhnha123099@gmail.com.
Commit
ci: upgrade to using macos-13
In April, GitHub announced that the `macos-13` pool is available:
https://github.blog/changelog/2023-04-24-github-actions-macos-13-is-now-available/.
It is only a matter of time until the `macos-12` pool is going away,
therefore we should switch now, without pressure of a looming deadline.

Since the `macos-13` runners no longer include Python2, we also drop
specifically testing with Python2 and switch uniformly to Python3, see
https://github.com/actions/runner-images/blob/HEAD/images/macos/macos-13-Readme.md
for details about the software available on the `macos-13` pool's
runners.

Also, on macOS 13, Homebrew seems to install a `gcc@9` package that no
longer comes with a regular `unistd.h` (there seems only to be a
`ssp/unistd.h`), and hence builds would fail with:

    In file included from base85.c:1:
    git-compat-util.h:223:10: fatal error: unistd.h: No such file or directory
      223 | #include <unistd.h>
          |          ^~~~~~~~~~
    compilation terminated.

The reason why we install GCC v9.x explicitly is historical, and back in
the days it was because it was the _newest_ version available via
Homebrew: 176441b (ci: build Git with GCC 9 in the 'osx-gcc' build
job, 2019-11-27).

To reinstate the spirit of that commit _and_ to fix that build failure,
let's switch to the now-newest GCC version: v13.x.

Signed-off-by: Johannes Schindelin <johannes.schindelin@gmx.de>
Signed-off-by: Junio C Hamano <gitster@pobox.com>
 master
 v2.43.0 
…
 v2.43.0-rc1
@dscho
@gitster
dscho authored and gitster committed on Nov 3, 2023 
1 parent 61a22dd
commit 682a868
 
Showing 2 changed files with 5 additions and 7 deletions.
Filter changed files
  6 changes: 3 additions & 3 deletions6  
.github/workflows/main.yml
@@ -276,11 +276,11 @@ jobs:
            pool: ubuntu-20.04
          - jobname: osx-clang
            cc: clang
            pool: macos-12
            pool: macos-13
          - jobname: osx-gcc
            cc: gcc
            cc_package: gcc-9
            pool: macos-12
            cc_package: gcc-13
            pool: macos-13
          - jobname: linux-gcc-default
            cc: gcc
            pool: ubuntu-latest
  6 changes: 2 additions & 4 deletions6  
ci/lib.sh
@@ -253,11 +253,9 @@ ubuntu-*)
	export PATH="$GIT_LFS_PATH:$P4_PATH:$PATH"
	;;
macos-*)
	if [ "$jobname" = osx-gcc ]
	MAKEFLAGS="$MAKEFLAGS PYTHON_PATH=$(which python3)"
	if [ "$jobname" != osx-gcc ]
	then
		MAKEFLAGS="$MAKEFLAGS PYTHON_PATH=$(which python3)"
	else
		MAKEFLAGS="$MAKEFLAGS PYTHON_PATH=$(which python2)"
		MAKEFLAGS="$MAKEFLAGS APPLE_COMMON_CRYPTO_SHA1=Yes"
	fi
	;;
0 comments on commit 682a868
@PhamThanhTu16A
Write
Preview
You need to verify your email address before you can join the conversation.

Footer
© 2024 GitHub, Inc.
Footer navigation
Terms
Privacy
Security
Status
Docs
Contact
Manage cookies
Do not share my personal information
ci: upgrade to using macos-13 · git/git@682a868
