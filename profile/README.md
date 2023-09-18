~~~~~~
Reset a project when `repo sync` failed:

(Lineage) $ rm -rf external/boringssl/ .repo/projects/external/boringssl.git/ .repo/project-objects/LineageOS/android_external_boringssl.git/
(Lineage) $ repo sync --force-sync external/boringssl

`repo sync -d` forces set the branch of each repo.

https://repo-discuss.narkive.com/J0oejcPR/how-can-i-switch-branch-after-i-do-repo-init

Gavin Fang11 years ago
Permalink
Post by m***@gmail.com
repo init -u https://android.googlesource.com/platform/manifest -b android-4.0.1_r1
But how can I
- find out what other branches are there for my repo
As Bjorn says: goto .repo/manifests and check with command "git branch -a"
- switch to another branch for my repo
For a clean work directory , I usually
1 repo init -b <branchname>
2 remove all the files except directory .repo
3 repo sync
Post by m***@gmail.com
Thank you.
--

LA.UM.9.1.r1-07300-SMxxx0.0
LA.UM.9.1.c11-56900-SMxxx0.QSSI13.0

How to merge qcom drivers: https://baalajimaestro.me/posts/kernel-for-newbies/

fw-api
techpack/video -- x
techpack/audio
qca-wifi-host-cmn
qcacld-3.0

https://git.codelinaro.org/clo/la/platform/vendor/qcom-opensource/wlan/qca-wifi-host-cmn LA.UM.9.1.r1-07300-SMxxx0.0

https://gist.github.com/Lacentix/b8d4cad389b0e0269cb4dd04d374755c

git remote add fw-api https://git.codelinaro.org/clo/la/platform/vendor/qcom-opensource/wlan/fw-api.git
git fetch fw-api LA.UM.9.1.r1-07300-SMxxx0.0
~ git merge -s ours --no-commit --allow-unrelated-histories FETCH_HEAD ~
git read-tree --prefix=drivers/net/wireless/qualcomm/wcn39xx/fw-api -u FETCH_HEAD
git commit


===

First use best_kernel to select the nearest CLO(CAF) tag for kernel and drivers.

git fetch clo 'refs/tags/*:refs/tags/*'
git add <samsung source>
git commit
best_kernel '*SMxxx0*'
best_kernel '*sm8150*'

kernel LA.UM.9.1.r1-07300-SMxxx0.0

fw-api: LA.UM.8.1.r1-15800-sm8150.0 (411 lines changed)
qca-wifi-host-cmn: LA.UM.9.1.r1-11800-SMxxx0.0 (9752; may not be the nearest version because version tag below 11700 is invalid?)
qcacld-3.0: LA.UM.9.1.r1-06700-SMxxx0.0 (6825)
techpack-audio: LA.UM.9.1.r1-07300-SMxxx0.0 (22424)

Check them out and place in proper position, and then apply samsung source on top of the source.

Then merge latest branch, e.g. aosp/clo/lineage qcom sm8150 kernel.

To merge Lineage android_kernel_qcom_sm8150:

git remote add los-kernel ~/work/lineage-dev/android/lineage/kernel/samsung/sm8150-common
# "sm8150-common" repository has a remote "los_qcom" pointing to "LineageOS/android_kernel_qcom_sm8150".
git fetch los-kernel refs/remotes/los_qcom/lineage-20
# git merge -X subtree=xxx is bidirectional
git merge -X subtree=drivers/staging/fw-api FETCH_HEAD

a() { git diff --color -U10 c4699032af4b37bb2a1a7a8b3f9394fb3a36b3d9~ c4699032af4b37bb2a1a7a8b3f9394fb3a36b3d9 "$1" | less -R ; }
b() { while true; do IFS='' read -e -p 'Input file name: ' -r f; a "$f"; done ; }
c() { git show LA.UM.9.1.r1-13500-SMxxx0.QSSI13.0:"$1"; }


a() { git diff --color -U10 G9700ZCU7HVJ2_atop_LA.UM.9.1.r1-07300-SMxxx0.0~ G9700ZCU7HVJ2_atop_LA.UM.9.1.r1-07300-SMxxx0.0 "$1" | less -R ; }
aa() { git diff --color -U10 G9700ZCU7HVJ2_atop_caf_merge_los~ G9700ZCU7HVJ2_atop_caf_merge_los "$1" | less -R ; }

diff2ver() { { echo; echo; echo =======; git show --color --oneline --decorate --no-patch "$1"; git show --color --oneline --decorate --no-patch "$2"; echo =======; echo; echo; git diff -U"${4:-20}" --color "$1" "$2" -- "$3"; } | less -R; }

# diff ? to HEAD along HEAD
d() { local hv; local vhash; hv=2; while true; do vhash="$(git log --full-history --pretty=format:"%h" --no-patch -"$hv" HEAD -- "$1" | tail -1)"; diff2ver "$vhash" HEAD "$1"; read -ep "hv=${hv}. Press enter to make hv++ and continue: "; hv=$(( hv + 1 )) ; done ; }

# diff ?-1 to ? along HEAD
e() { local hv; local vhash; hv=1; while true; do vhash="$(git log --full-history --pretty=format:"%h" --no-patch -"$hv" HEAD -- "$1" | tail -1)"; diff2ver "$vhash"~ "$vhash" "$1"; read -ep "hv=${hv}. Press enter to make hv++ and continue: "; hv=$(( hv + 1 )) ; done ; }

# diff G9700ZCU7HVJ2_atop_LA.UM.9.1.r1-07300-SMxxx0.0~ to ? along HEAD
f() { local hv; local vhash; hv=2; while true; do vhash="$(git log --full-history --pretty=format:"%h" --no-patch -"$hv" HEAD -- "$1" | tail -1)"; diff2ver G9700ZCU7HVJ2_atop_LA.UM.9.1.r1-07300-SMxxx0.0~ "$vhash" "$1"; read -ep "hv=${hv}. Press enter to make hv++ and continue: "; hv=$(( hv + 1 )) ; done ; }

# diff los_qcom/lineage-20 to HEAD
g() { diff2ver los_qcom/lineage-20 HEAD "$1"; }

# diff ? to los_qcom/lineage-20 along los_qcom/lineage-20
d() { local hv; local vhash; hv=2; while true; do vhash="$(git log --full-history --pretty=format:"%h" --no-patch -"$hv" los_qcom/lineage-20 -- "$1" | tail -1)"; diff2ver "$vhash" los_qcom/lineage-20 "$1"; read -ep "hv=${hv}. Press enter to make hv++ and continue: "; hv=$(( hv + 1 )) ; done ; }

brunch beyond0qlte ; echo -e '\a'; sleep 0.5; echo -e '\a'

tools/extract-utils/extract_utils.sh
~~~
function get_file() {
    local SRC="$3"

    if [ "$SRC" = "adb" ]; then
        # try to pull
        adb shell "su -c 'cat \"$1\"'" > "$2" 2>/dev/null && chmod `adb shell "su -c 'stat -c %a \"$1\"'"` "$2" 2>/dev/null && return 0
        adb shell "su -c 'cat \"${1#/system}\"'" > "$2" 2>/dev/null && chmod `adb shell "su -c 'stat -c %a \"${1#/system}\"'"` "$2" 2>/dev/null && return 0
        adb shell "su -c 'cat \"system/$1\"'" > "$2" 2>/dev/null && chmod `adb shell "su -c 'stat -c %a \"system/$1\"'"` "$2" 2>/dev/null && return 0
        # adb pull "$1" "$2" >/dev/null 2>&1 && return 0
        # adb pull "${1#/system}" "$2" >/dev/null 2>&1 && return 0
        # adb pull "system/$1"    "$2" >/dev/null 2>&1 && return 0
~~~
~~~~~~
