# GitHub Actions to Free Disk Space on Ubuntu runners

A customizable GitHub Actions to free disk space on Linux GitHub Actions runners.

On a typical Ubuntu runner, with all options turned on (or not turned off rather), this can clear up to 45 GB of disk space in about 3 minutes (the longest period is calling `apt` to uninstall packages). This is useful when you need a lot of disk space to run computations.

Please don't hesitate to [submit issues](https://github.com/gruve-p/free-disk-space/issues) to report problems or suggest new features (or sets of files to help remove).

Also, please ⭐️ the repo if you like this GitHub Actions! Thanks! 😊

## Example

```yaml
name: Free Disk Space (Ubuntu)
on: push

jobs:
  free-disk-space:
    runs-on: ubuntu-latest
    steps:

    - name: Free Disk Space (Ubuntu)
      uses: gruve-p/free-disk-space@main
      with:
        # this might remove tools that are actually needed,
        # if set to "true" but frees about 6 GB
        tool-cache: true

        # all of these default to true, but feel free to set to
        # "false" if necessary for your workflow
        android: true
        dotnet: true
        haskell: true
        docker-images: true
        codeql-images: true        
        large-packages: true
        docker-images: true
        swap-storage: true
```
## Options

Most of the options are self-explanatory.

The option `tool-cache` removes all the pre-cached tools (Node, Go, Python, Ruby, ...) that are loaded in a runner's environment, [installed in the path specified by the `AGENT_TOOLSDIRECTORY` environment variable](https://github.com/actions/virtual-environments/blob/5a2cb18a48bce5da183486b95f5494e4fd0c0640/images/linux/scripts/installers/configure-environment.sh#L25-L29) (the same environment variable is used across Windows/macOS/Linux runners, see an example of its use on [the `setup-python` GitHub Action](https://github.com/actions/setup-python)). This option was [suggested](https://github.com/actions/virtual-environments/issues/2875#issuecomment-1163392159) by [@miketimofeev](https://github.com/miketimofeev).

## Acknowledgement

This GitHub Actions came around because I kept rewriting the same few lines of `rm -rf` code.

Here are a few sources of inspiration:
- https://github.community/t/bigger-github-hosted-runners-disk-space/17267/11
- https://github.com/apache/flink/blob/master/tools/azure-pipelines/free_disk_space.sh
- https://github.com/ShubhamTatvamasi/free-disk-space-action
- https://github.com/actions/virtual-environments/issues/2875#issuecomment-1163392159
- https://github.com/easimon/maximize-build-space/

## Typical Output

The amount of space storage saved by each option on an `ubuntu-22.04.2` runner is summarized here:
```
=> Android library: Saved 12GiB
=> .NET runtime: Saved 1.7GiB
=> Haskell runtime: Saved 4.6GiB
=> Docker images: Saved 3.6GiB
=> Codeql images: Saved 5.2GiB
=> Large misc. packages: Saved 11GiB
=> Tool cache: Saved 3.2GiB
=> Swap storage: Saved 4.0GiB

Total: Saved 45GiB
```

The log output of a typical example (with all options set to `true`) looks like this:
```
================================================================================
BEFORE CLEAN-UP:

$ df -h /

Filesystem      Size  Used Avail Use% Mounted on
/dev/root        84G   58G   26G  69% /
$ df -a /

Filesystem     1K-blocks     Used Available Use% Mounted on
/dev/root       87204404 59932140  27255880  69% /
$ df -a

Filesystem     1K-blocks     Used Available Use% Mounted on
/dev/root       87204404 59932140  27255880  69% /
devtmpfs         3550836        0   3550836   0% /dev
proc                   0        0         0    - /proc
sysfs                  0        0         0    - /sys
securityfs             0        0         0    - /sys/kernel/security
tmpfs            3555304      172   3555132   1% /dev/shm
devpts                 0        0         0    - /dev/pts
tmpfs            1422124     1092   1421032   1% /run
tmpfs               5120        0      5120   0% /run/lock
cgroup2                0        0         0    - /sys/fs/cgroup
pstore                 0        0         0    - /sys/fs/pstore
bpf                    0        0         0    - /sys/fs/bpf
systemd-1              -        -         -    - /proc/sys/fs/binfmt_misc
hugetlbfs              0        0         0    - /dev/hugepages
mqueue                 0        0         0    - /dev/mqueue
debugfs                0        0         0    - /sys/kernel/debug
tracefs                0        0         0    - /sys/kernel/tracing
fusectl                0        0         0    - /sys/fs/fuse/connections
configfs               0        0         0    - /sys/kernel/config
none                   0        0         0    - /run/credentials/systemd-sysusers.service
/dev/loop0         65024    65024         0 100% /snap/core20/1891
/dev/loop1        114688   114688         0 100% /snap/lxd/24322
/dev/loop2         54656    54656         0 100% /snap/snapd/19361
/dev/sda15        106858     6182    100677   6% /boot/efi
binfmt_misc            0        0         0    - /proc/sys/fs/binfmt_misc
/dev/sdb1       14341128  4194336   9396508  31% /mnt
tmpfs             711060       12    711048   1% /run/user/1001
tmpfs            1422124     1092   1421032   1% /run/snapd/ns
nsfs                   0        0         0    - /run/snapd/ns/lxd.mnt
================================================================================


********************************************************************************
=> Android library: Saved 12GiB
********************************************************************************


********************************************************************************
=> .NET runtime: Saved 1.7GiB
********************************************************************************


********************************************************************************
=> Haskell runtime: Saved 4.6GiB
********************************************************************************

Deleted Images:
untagged: ubuntu:20.04
untagged: ubuntu@sha256:db8bf6f4fb351aa7a26e27ba2686cf35a6a409f65603e59d4c203e58387dc6b3
deleted: sha256:88bd6891718934e63638d9ca0ecee018e69b638270fe04990a310e5c78ab4a92
deleted: sha256:6f37ca73c74f2cef0ddefd960260f2033c16c84583c5507a4f37b1cf7631dc20
untagged: alpine:3.17
untagged: alpine@sha256:124c7d2707904eea7431fffe91522a01e5a861a624ee31d03372cc1d138a3126
deleted: sha256:9ed4aefc74f6792b5a804d1d146fe4b4a2299147b0f50eaf2b08435d7b38c27e
untagged: node:16
untagged: node@sha256:2ed6bba040f90005db9785927689c0d9a9442ca2cf9a59dc52297d684285c094
deleted: sha256:4fbbe8e45ea152e06c94e848a410983bf047cbf78bf3c71e5d486f17433810ba
deleted: sha256:601810337772ec2ef69155c10418c2f253400df3335b2b26d499cc88f20414a8
deleted: sha256:67a64c7a04d12e6b323f45b8af5d90c97502ddbc07536ea8402f1f2609f28b28
deleted: sha256:c665ca5103420383cdaf8167d8f9fc4ccdd185d43413dcb318fd11d18ce8e3ae
deleted: sha256:402cd209bb6db02ad811801d20291c796e58bd0833260d7a5e414a341e267f13
untagged: debian:10
untagged: debian@sha256:322859539526a04978dee1a2575b515295f6706f17d280f877a634299a7c1414
deleted: sha256:8b5601a5a7f855241ac7f372ec0042e793b0b3eb3f3a601014845f22bd371c90
untagged: buildpack-deps:bullseye
untagged: buildpack-deps@sha256:1b8ef32e6ebc10abab974c91d1c4659b3cda831bf2938ae857cdd2d36f58b1d8
deleted: sha256:0d64d7665471654595e50271be85ec3e1839ca581735de8cf58d4d5bdd4bd63f
untagged: node:14
untagged: node@sha256:a158d3b9b4e3fa813fa6c8c590b8f0a860e015ad4e59bbce5744d2f6fd8461aa
deleted: sha256:1d12470fa662a2a5cb50378dcdc8ea228c1735747db410bbefb8e2d9144b5452
deleted: sha256:d7f4fb376c48badb9879c97df87580480be3f4dcad2e2f33425792d164933fa3
deleted: sha256:71fe964a0bdce3b1795d90eb1f8a501dd719e8612faeb40783706c962339c75c
deleted: sha256:559303f34408584d79e77eab1ec15eb80d04d753a87a38c8da31542440c0a64a
deleted: sha256:483ca60b49ed28025355f381d9e2b759e67e66b9dfb266c66f57acf0cb47033e
deleted: sha256:f1edb12649962bbc5048b132c2201e98d474b9f99e6936d41633b43d6e7bd91d
deleted: sha256:9f9d995a1674f94b553c90e4eb3502db04a8220403f3add46486659752a3a108
deleted: sha256:a61e818f0b1f7522e3a27211026f564f1fd98ae2034229d1233623e92a6a7961
deleted: sha256:16c88c4e67dd8ed4c67c0e9c1dae7df697f4607eab4c3d2f12b1b18206a6a6e4
deleted: sha256:b2dba74777543b60e1a5be6da44e67659f51b8df1e96922205a5dde6b92dda3c
untagged: alpine:3.16
untagged: alpine@sha256:c2b622f6e510a0d25bccaffa9e67b75a6860cb09b74bb58cfc36a9ef4331109f
deleted: sha256:8471affe5de5c05c938d681f28e9b9139df00fd479da775caf217886b12bfa61
deleted: sha256:5bc340f6d4f5a3bc999dfbc790a0bdf0920b9103ef794645034de4260ee4e9c8
untagged: ubuntu:22.04
untagged: ubuntu@sha256:ac58ff7fe25edc58bdf0067ca99df00014dbd032e2246d30a722fa348fd799a5
deleted: sha256:1f6ddc1b2547b2e38dc25b265ac585238a3c23da63976722864dab2a069c74f4
deleted: sha256:966e94ab6e166fb358a208cfd8169d22dea352501c96700eb7f45092a2962ee6
untagged: buildpack-deps:buster
untagged: buildpack-deps@sha256:c15924ddbee8cbaac4ab82d735499f8e3fb14be38943544e3c8333c089c05485
deleted: sha256:f7fdc760ba6cbb443832f591462bcc94a0f7b105b126556263dcc0f3f9a7ba1e
deleted: sha256:9f9ca3f8c805eca927cbf92ab6feaaf32eea34403440c632fb0e4e5709ae32a7
deleted: sha256:1e0749efe502f6792af7dffeaa1748d9184e48d92ee5f00e997e06bda0175c89
deleted: sha256:2fdd12d748b410bf33a4155b93db0fd11d940fe9ba763f2f29e5179e15480b5a
deleted: sha256:c7e59965a0b4c8c0f57fb5eb9c93735ef658d05243a4947f7c4ab51cfd055b9b
untagged: ubuntu:18.04
untagged: ubuntu@sha256:152dc042452c496007f07ca9127571cb9c29697f42acbfad72324b2bb2e43c98
deleted: sha256:f9a80a55f492e823bf5d51f1bd5f87ea3eed1cb31788686aa99a2fb61a27af6a
deleted: sha256:548a79621a426b4eb077c926eabac5a8620c454fb230640253e1b44dc7dd7562
untagged: node:18-alpine
untagged: node@sha256:f41850f74ff16a33daff988e2ea06ef8f5daeb6fb84913c7df09552a98caba09
deleted: sha256:9752a2a439336b2008ad3c9aab054fd725c5b07c502a960e1d1506767fb463ca
deleted: sha256:85512fef099bfdd0db5bb896b5febdbe7d4542e58ab3058c69dccd26b1d77ec4
deleted: sha256:245683b16d4972fc34fdd1d18cfcd90abaf0924e34599059513e2a5e26e06b82
deleted: sha256:8d808154a6dbcc06255df77aff30543eb8701a80f78cff35248be0d8bc17a260
untagged: debian:11
untagged: debian@sha256:432f545c6ba13b79e2681f4cc4858788b0ab099fc1cca799cc0fae4687c69070
deleted: sha256:1ac99357ef2152701f790e2d0112f4295367906a349f2d85cbcd43b8a1c74a88
untagged: node:14-alpine
untagged: node@sha256:434215b487a329c9e867202ff89e704d3a75e554822e07f3e0c0f9e606121b33
deleted: sha256:0dac3dc27b1ad570e6c3a7f7cd29e88e7130ff0cad31b2ec5a0f222fbe971bdb
deleted: sha256:3f3a6e7f9c9d353391c75899b353f0f14c203e98fdbd0fd11bf6b84a74dab522
deleted: sha256:dee85e6b7f481bd5bbdff9fdfd8568ee70adb0d277f352348ef099f52afb46df
deleted: sha256:d6c83f14fe35dff122c995ada9da84a33ad2b7bf357133c3fa4c2baea3d3755b
untagged: node:16-alpine
untagged: node@sha256:bca38851a77922e849907c57588bdf3dfcf95725186d7c26cdfd64da730989d3
deleted: sha256:95f54ffb5ed5d7ebd8c79e1e394e8b740ced9176efc851047c79d1493d0a0330
deleted: sha256:c545fb8d1453b3313ab1ce6712139c3cf4e2c394dbaf8e26f432ed7a311bf534
deleted: sha256:8e7ffa919180f1e6c0729e164e2c28865a1eec171776bda62b6dae8ded8eeaf7
deleted: sha256:4921767d33acbb0e41bda92297d7a80dea39a4155ff21cdd01221b55cbdb845f
deleted: sha256:bb01bd7e32b58b6694c8c3622c230171f1cec24001a82068a8d30d338f420d6c
untagged: node:18
untagged: node@sha256:8cdf7234449f35e579f7491137807ddb3a089f028862f7ab69af437cc9f47ff1
deleted: sha256:78b037dbb659142a12986b522897824ab60209d194a336c15a5006a55fffe9b0
deleted: sha256:ffac3b26ccf8839d8609323e54e03b3080a782dc9086d22e78e28027af47cf06
deleted: sha256:e94c0ce26d5c9583a33b6d04706207abef6b23a66fd6fdb9065d76d54cee520b
deleted: sha256:bfcea0db8194b81693ea28016d05f1851d79116786267df49e51e14895f6e385
deleted: sha256:125548a4b496f8543755a30154a3a3df69ee19411f80cd035a3e00246a4f5b97
deleted: sha256:36feb0901e7f619e7ade3b5b03fb2052a2475c4ad957209c2c08cf632350ef53
deleted: sha256:a3272a636fa0f34dcb0e73163da955d3add60a99ebacf9c37bfa28bb86dca6af
deleted: sha256:d8ca3862f8d4bd7ea0f417ed455f201a50a8c9cec242cc5de0abaed7c86d896a
deleted: sha256:974e52a24adf98b37b8895f61b8d8d273b73a8596b80da491342936fa1751541
untagged: moby/buildkit:latest
untagged: moby/buildkit@sha256:d6fa89830c26919acba23c5cafa09df0c3ec1fbde20bb2a15ff349e0795241f4
deleted: sha256:152ba3e57b496876d5f734e3e44e26d68a31587a129d54e953f7c4bce8275de5
deleted: sha256:bed0695593391ff5ec6b4a462a29f43a63cee77d03c0e17aee51608491c0f201
deleted: sha256:bd14a3f7ceda3634f0a40052017ead8311ff1bef9bb24a363c40fe7a3d14566b
deleted: sha256:34f77dba7602116884167c25b14ca7b36ff4c85161a70b9615b554fd2d2d2e4e
deleted: sha256:f1417ff83b319fbdae6dd9cd6d8c9c88002dcd75ecf6ec201c8c6894681cf2b5
untagged: alpine:3.15
untagged: alpine@sha256:ecbdce53b2c2f43ab1b19418bcbd3f120a23547d9497030c8d978114512b883e
deleted: sha256:58c0644b45ff503f1b05dc272ce691659daf2ea2c940bd18e7efbf30f93427f7
deleted: sha256:49f62469da7651bcaf2d76af7a56680c2dd9b7cf177ead3e8a675b160500b68e
untagged: alpine:3.14
untagged: alpine@sha256:0f2d5c38dd7a4f4f733e688e3a6733cb5ab1ac6e3cb4603a5dd564e5bfb80eed
deleted: sha256:9e179bacf43c4d3428d57cf459799ba0285b901945f9eccb17b6da056d3532c7
deleted: sha256:9733ccc395133a067f01ee6e380003d80fe9f443673e0f992ae6a4a7860a872c

Total reclaimed space: 3.612GB

********************************************************************************
=> Docker images: Saved 3.6GiB
********************************************************************************


********************************************************************************
=> Codeql images: Saved 5.2GiB
********************************************************************************

Successfully installed thor-1.2.2
Successfully installed nokogiri-1.14.5-x86_64-linux
Successfully installed apt-spy2-0.8.2
Parsing documentation for thor-1.2.2
Installing ri documentation for thor-1.2.2
Parsing documentation for nokogiri-1.14.5-x86_64-linux
Installing ri documentation for nokogiri-1.14.5-x86_64-linux
Parsing documentation for apt-spy2-0.8.2
Installing ri documentation for apt-spy2-0.8.2
Done installing documentation for thor, nokogiri, apt-spy2 after 3 seconds
3 gems installed
The closest mirror is: https://mirror.enzu.com/ubuntu/
Updating /etc/apt/sources.list
Updated '/etc/apt/sources.list' with https://mirror.enzu.com/ubuntu/
Run `apt-get update` to update
Get:1 https://packages.microsoft.com/ubuntu/22.04/prod jammy InRelease [3611 B]
Get:2 https://packages.microsoft.com/ubuntu/22.04/prod jammy/main amd64 Packages [69.3 kB]
Get:3 https://packages.microsoft.com/ubuntu/22.04/prod jammy/main arm64 Packages [14.8 kB]
Get:4 https://packages.microsoft.com/ubuntu/22.04/prod jammy/main armhf Packages [7897 B]
Hit:5 https://ppa.launchpadcontent.net/ubuntu-toolchain-r/test/ubuntu jammy InRelease
Get:6 https://mirror.enzu.com/ubuntu jammy InRelease [270 kB]
Get:7 https://mirror.enzu.com/ubuntu jammy-updates InRelease [114 kB]
Get:8 https://mirror.enzu.com/ubuntu jammy-backports InRelease [99.8 kB]
Get:9 https://mirror.enzu.com/ubuntu jammy-security InRelease [110 kB]
Get:10 https://mirror.enzu.com/ubuntu jammy/main amd64 Packages [1395 kB]
Get:11 https://mirror.enzu.com/ubuntu jammy/main Translation-en [510 kB]
Get:12 https://mirror.enzu.com/ubuntu jammy/main amd64 c-n-f Metadata [30.3 kB]
Get:13 https://mirror.enzu.com/ubuntu jammy/restricted amd64 Packages [129 kB]
Get:14 https://mirror.enzu.com/ubuntu jammy/restricted Translation-en [18.6 kB]
Get:15 https://mirror.enzu.com/ubuntu jammy/restricted amd64 c-n-f Metadata [488 B]
Get:16 https://mirror.enzu.com/ubuntu jammy/universe amd64 Packages [14.1 MB]
Get:17 https://mirror.enzu.com/ubuntu jammy/universe Translation-en [5652 kB]
Get:18 https://mirror.enzu.com/ubuntu jammy/universe amd64 c-n-f Metadata [286 kB]
Get:19 https://mirror.enzu.com/ubuntu jammy/multiverse amd64 Packages [217 kB]
Get:20 https://mirror.enzu.com/ubuntu jammy/multiverse Translation-en [112 kB]
Get:21 https://mirror.enzu.com/ubuntu jammy/multiverse amd64 c-n-f Metadata [8372 B]
Get:22 https://mirror.enzu.com/ubuntu jammy-updates/main amd64 Packages [839 kB]
Get:23 https://mirror.enzu.com/ubuntu jammy-updates/main Translation-en [186 kB]
Get:24 https://mirror.enzu.com/ubuntu jammy-updates/main amd64 c-n-f Metadata [12.3 kB]
Get:25 https://mirror.enzu.com/ubuntu jammy-updates/restricted amd64 Packages [566 kB]
Get:26 https://mirror.enzu.com/ubuntu jammy-updates/restricted Translation-en [87.1 kB]
Get:27 https://mirror.enzu.com/ubuntu jammy-updates/restricted amd64 c-n-f Metadata [556 B]
Get:28 https://mirror.enzu.com/ubuntu jammy-updates/universe amd64 Packages [792 kB]
Get:29 https://mirror.enzu.com/ubuntu jammy-updates/universe Translation-en [139 kB]
Get:30 https://mirror.enzu.com/ubuntu jammy-updates/universe amd64 c-n-f Metadata [14.9 kB]
Get:31 https://mirror.enzu.com/ubuntu jammy-updates/multiverse amd64 Packages [7988 B]
Get:32 https://mirror.enzu.com/ubuntu jammy-updates/multiverse Translation-en [2448 B]
Get:33 https://mirror.enzu.com/ubuntu jammy-updates/multiverse amd64 c-n-f Metadata [432 B]
Get:34 https://mirror.enzu.com/ubuntu jammy-backports/main amd64 Packages [3324 B]
Get:35 https://mirror.enzu.com/ubuntu jammy-backports/main Translation-en [1580 B]
Get:36 https://mirror.enzu.com/ubuntu jammy-backports/main amd64 c-n-f Metadata [272 B]
Get:37 https://mirror.enzu.com/ubuntu jammy-backports/restricted amd64 c-n-f Metadata [116 B]
Get:38 https://mirror.enzu.com/ubuntu jammy-backports/universe amd64 Packages [6744 B]
Get:39 https://mirror.enzu.com/ubuntu jammy-backports/universe Translation-en [9460 B]
Get:40 https://mirror.enzu.com/ubuntu jammy-backports/universe amd64 c-n-f Metadata [352 B]
Get:41 https://mirror.enzu.com/ubuntu jammy-backports/multiverse amd64 c-n-f Metadata [116 B]
Get:42 https://mirror.enzu.com/ubuntu jammy-security/main amd64 Packages [593 kB]
Get:43 https://mirror.enzu.com/ubuntu jammy-security/main Translation-en [125 kB]
Get:44 https://mirror.enzu.com/ubuntu jammy-security/main amd64 c-n-f Metadata [7800 B]
Get:45 https://mirror.enzu.com/ubuntu jammy-security/restricted amd64 Packages [528 kB]
Get:46 https://mirror.enzu.com/ubuntu jammy-security/restricted Translation-en [81.2 kB]
Get:47 https://mirror.enzu.com/ubuntu jammy-security/restricted amd64 c-n-f Metadata [556 B]
Get:48 https://mirror.enzu.com/ubuntu jammy-security/universe amd64 Packages [634 kB]
Get:49 https://mirror.enzu.com/ubuntu jammy-security/universe Translation-en [86.4 kB]
Get:50 https://mirror.enzu.com/ubuntu jammy-security/universe amd64 c-n-f Metadata [11.3 kB]
Get:51 https://mirror.enzu.com/ubuntu jammy-security/multiverse amd64 Packages [4268 B]
Get:52 https://mirror.enzu.com/ubuntu jammy-security/multiverse Translation-en [972 B]
Get:53 https://mirror.enzu.com/ubuntu jammy-security/multiverse amd64 c-n-f Metadata [228 B]
Fetched 27.9 MB in 5s (5604 kB/s)
Reading package lists...
Reading package lists...
Building dependency tree...
Reading state information...
Package 'dotnet-nightly' is not installed, so not removed
Package 'dotnet-targeting-pack-3.1' is not installed, so not removed
Package 'dotnet-sdk-6.0-source-built-artifacts' is not installed, so not removed
Package 'dotnet-templates-6.0' is not installed, so not removed
Package 'dotnet-apphost-pack-3.1' is not installed, so not removed
Package 'dotnet-runtime-3.1' is not installed, so not removed
Package 'dotnet-sdk-3.1' is not installed, so not removed
Package 'dotnet-hostfxr-3.1' is not installed, so not removed
Package 'dotnet-runtime-deps-3.1' is not installed, so not removed
The following additional packages will be installed:
  aspnetcore-targeting-pack-6.0
The following packages will be REMOVED:
  aspnetcore-runtime-6.0 aspnetcore-runtime-7.0 aspnetcore-targeting-pack-7.0
  dotnet-apphost-pack-6.0 dotnet-apphost-pack-7.0 dotnet-host
  dotnet-hostfxr-6.0 dotnet-hostfxr-7.0 dotnet-runtime-6.0 dotnet-runtime-7.0
  dotnet-runtime-deps-6.0 dotnet-runtime-deps-7.0 dotnet-sdk-6.0
  dotnet-sdk-7.0 dotnet-targeting-pack-6.0 dotnet-targeting-pack-7.0
The following packages will be upgraded:
  aspnetcore-targeting-pack-6.0
1 upgraded, 0 newly installed, 16 to remove and 21 not upgraded.
Need to get 1455 kB of archives.
After this operation, 1002 MB disk space will be freed.
Get:1 https://mirror.enzu.com/ubuntu jammy-updates/universe amd64 aspnetcore-targeting-pack-6.0 amd64 6.0.113-0ubuntu1~22.04.1 [1455 kB]
Fetched 1455 kB in 1s (2187 kB/s)
(Reading database ...
(Reading database ... 5%
(Reading database ... 10%
(Reading database ... 15%
(Reading database ... 20%
(Reading database ... 25%
(Reading database ... 30%
(Reading database ... 35%
(Reading database ... 40%
(Reading database ... 45%
(Reading database ... 50%
(Reading database ... 55%
(Reading database ... 60%
(Reading database ... 65%
(Reading database ... 70%
(Reading database ... 75%
(Reading database ... 80%
(Reading database ... 85%
(Reading database ... 90%
(Reading database ... 95%
(Reading database ... 100%
(Reading database ... 221043 files and directories currently installed.)
Removing dotnet-sdk-6.0 (6.0.408-1) ...
Removing aspnetcore-runtime-6.0 (6.0.16-1) ...
Removing dotnet-sdk-7.0 (7.0.302-1) ...
Removing aspnetcore-runtime-7.0 (7.0.5-1) ...
Removing aspnetcore-targeting-pack-7.0 (7.0.5-1) ...
Removing dotnet-apphost-pack-6.0 (6.0.16-1) ...
Removing dotnet-apphost-pack-7.0 (7.0.5-1) ...
Removing dotnet-runtime-6.0 (6.0.16-1) ...
Removing dotnet-hostfxr-6.0 (6.0.16-1) ...
Removing dotnet-runtime-7.0 (7.0.5-1) ...
Removing dotnet-hostfxr-7.0 (7.0.5-1) ...
Removing dotnet-host (7.0.5-1) ...
Removing dotnet-runtime-deps-6.0 (6.0.16-1) ...
Removing dotnet-runtime-deps-7.0 (7.0.5-1) ...
(Reading database ...
(Reading database ... 5%
(Reading database ... 10%
(Reading database ... 15%
(Reading database ... 20%
(Reading database ... 25%
(Reading database ... 30%
(Reading database ... 35%
(Reading database ... 40%
(Reading database ... 45%
(Reading database ... 50%
(Reading database ... 55%
(Reading database ... 60%
(Reading database ... 65%
(Reading database ... 70%
(Reading database ... 75%
(Reading database ... 80%
(Reading database ... 85%
(Reading database ... 90%
(Reading database ... 95%
(Reading database ... 100%
(Reading database ... 213881 files and directories currently installed.)
Preparing to unpack .../aspnetcore-targeting-pack-6.0_6.0.113-0ubuntu1~22.04.1_amd64.deb ...
Unpacking aspnetcore-targeting-pack-6.0 (6.0.113-0ubuntu1~22.04.1) over (6.0.16-1) ...
(Reading database ...
(Reading database ... 5%
(Reading database ... 10%
(Reading database ... 15%
(Reading database ... 20%
(Reading database ... 25%
(Reading database ... 30%
(Reading database ... 35%
(Reading database ... 40%
(Reading database ... 45%
(Reading database ... 50%
(Reading database ... 55%
(Reading database ... 60%
(Reading database ... 65%
(Reading database ... 70%
(Reading database ... 75%
(Reading database ... 80%
(Reading database ... 85%
(Reading database ... 90%
(Reading database ... 95%
(Reading database ... 100%
(Reading database ... 213882 files and directories currently installed.)
Removing dotnet-targeting-pack-6.0 (6.0.16-1) ...
Removing dotnet-targeting-pack-7.0 (7.0.5-1) ...
Setting up aspnetcore-targeting-pack-6.0 (6.0.113-0ubuntu1~22.04.1) ...
Processing triggers for man-db (2.10.2-1) ...
NEEDRESTART-VER: 3.5
NEEDRESTART-KCUR: 5.15.0-1039-azure
NEEDRESTART-KEXP: 5.15.0-1039-azure
NEEDRESTART-KSTA: 1
Reading package lists...
Building dependency tree...
Reading state information...
Package 'llvm-11' is not installed, so not removed
Package 'llvm-11-dev' is not installed, so not removed
Package 'llvm-11-doc' is not installed, so not removed
Package 'llvm-11-examples' is not installed, so not removed
Package 'llvm-11-linker-tools' is not installed, so not removed
Package 'llvm-11-runtime' is not installed, so not removed
Package 'llvm-11-tools' is not installed, so not removed
Package 'llvm-12-doc' is not installed, so not removed
Package 'llvm-12-examples' is not installed, so not removed
Package 'llvm-14-doc' is not installed, so not removed
Package 'llvm-14-examples' is not installed, so not removed
Package 'llvm-dev' is not installed, so not removed
Package 'llvm-runtime' is not installed, so not removed
Package 'llvm-spirv' is not installed, so not removed
Package 'llvm-spirv-12' is not installed, so not removed
Package 'llvm-13-doc' is not installed, so not removed
Package 'llvm-13-examples' is not installed, so not removed
The following packages will be REMOVED:
  clang-12 clang-13 clang-14 clang-tidy-12 clang-tidy-13 clang-tidy-14
  clang-tools-12 clang-tools-13 clang-tools-14 lldb-14 llvm-12 llvm-12-dev
  llvm-12-linker-tools llvm-12-runtime llvm-12-tools llvm-13 llvm-13-dev
  llvm-13-linker-tools llvm-13-runtime llvm-13-tools llvm-14 llvm-14-dev
  llvm-14-linker-tools llvm-14-runtime llvm-14-tools
0 upgraded, 0 newly installed, 25 to remove and 21 not upgraded.
After this operation, 1052 MB disk space will be freed.
(Reading database ...
(Reading database ... 5%
(Reading database ... 10%
(Reading database ... 15%
(Reading database ... 20%
(Reading database ... 25%
(Reading database ... 30%
(Reading database ... 35%
(Reading database ... 40%
(Reading database ... 45%
(Reading database ... 50%
(Reading database ... 55%
(Reading database ... 60%
(Reading database ... 65%
(Reading database ... 70%
(Reading database ... 75%
(Reading database ... 80%
(Reading database ... 85%
(Reading database ... 90%
(Reading database ... 95%
(Reading database ... 100%
(Reading database ... 213205 files and directories currently installed.)
Removing clang-tidy-12 (1:12.0.1-19ubuntu3) ...
Removing clang-tools-12 (1:12.0.1-19ubuntu3) ...
Removing clang-12 (1:12.0.1-19ubuntu3) ...
Removing clang-tidy-13 (1:13.0.1-2ubuntu2.1) ...
Removing clang-tools-13 (1:13.0.1-2ubuntu2.1) ...
Removing clang-13 (1:13.0.1-2ubuntu2.1) ...
Removing clang-tidy-14 (1:14.0.0-1ubuntu1) ...
Removing clang-tools-14 (1:14.0.0-1ubuntu1) ...
Removing clang-14 (1:14.0.0-1ubuntu1) ...
Removing lldb-14 (1:14.0.0-1ubuntu1) ...
Removing llvm-12-dev (1:12.0.1-19ubuntu3) ...
Removing llvm-12 (1:12.0.1-19ubuntu3) ...
Removing llvm-12-linker-tools (1:12.0.1-19ubuntu3) ...
Removing llvm-12-runtime (1:12.0.1-19ubuntu3) ...
Removing llvm-12-tools (1:12.0.1-19ubuntu3) ...
Removing llvm-13-dev (1:13.0.1-2ubuntu2.1) ...
Removing llvm-13 (1:13.0.1-2ubuntu2.1) ...
Removing llvm-13-linker-tools (1:13.0.1-2ubuntu2.1) ...
Removing llvm-13-runtime (1:13.0.1-2ubuntu2.1) ...
Removing llvm-13-tools (1:13.0.1-2ubuntu2.1) ...
Removing llvm-14-dev (1:14.0.0-1ubuntu1) ...
Removing llvm-14 (1:14.0.0-1ubuntu1) ...
Removing llvm-14-linker-tools (1:14.0.0-1ubuntu1) ...
Removing llvm-14-runtime (1:14.0.0-1ubuntu1) ...
Removing llvm-14-tools (1:14.0.0-1ubuntu1) ...
Processing triggers for man-db (2.10.2-1) ...
Processing triggers for libc-bin (2.35-0ubuntu3.1) ...
Reading package lists...
Building dependency tree...
Reading state information...
Package 'php-lua' is not installed, so not removed
Package 'php-mysqlnd-ms' is not installed, so not removed
Package 'php-radius' is not installed, so not removed
Package 'php-stomp' is not installed, so not removed
Package 'php5.6-common' is not installed, so not removed
Package 'php5.6-json' is not installed, so not removed
Package 'php7.0-common' is not installed, so not removed
Package 'php7.1-common' is not installed, so not removed
Package 'php7.2-common' is not installed, so not removed
Package 'php7.3-common' is not installed, so not removed
Package 'php7.4-common' is not installed, so not removed
Package 'php8.0-common' is not installed, so not removed
Package 'php-pear-frontend-gtk' is not installed, so not removed
Package 'php-pear-frontend-web' is not installed, so not removed
Package 'php7.0-curl' is not installed, so not removed
Package 'php7.2-sodium' is not installed, so not removed
Package 'php5-cli' is not installed, so not removed
Package 'php5-curl' is not installed, so not removed
Package 'php-mcrypt' is not installed, so not removed
Package 'php5-mysql' is not installed, so not removed
Package 'php5-pgsql' is not installed, so not removed
Package 'php5-sqlite' is not installed, so not removed
Package 'php5' is not installed, so not removed
Package 'php5-ldap' is not installed, so not removed
Package 'php-mapscript' is not installed, so not removed
Package 'php-apc' is not installed, so not removed
Package 'php-suhosin' is not installed, so not removed
Package 'libapache2-mod-php5' is not installed, so not removed
Package 'php5-fpm' is not installed, so not removed
Package 'libapache2-mod-suphp' is not installed, so not removed
Package 'libgv-php5' is not installed, so not removed
Package 'libow-php5' is not installed, so not removed
Package 'php-hayageek-oauth2-yahoo' is not installed, so not removed
Package 'php-league-oauth2-google' is not installed, so not removed
Package 'php-stevenmaguire-oauth2-microsoft' is not installed, so not removed
Package 'phpphylotree' is not installed, so not removed
Package 'php-xcache' is not installed, so not removed
Package 'php-async-aws-s3' is not installed, so not removed
Package 'php-console-color2' is not installed, so not removed
Package 'php-alcaeus-mongo-php-adapter' is not installed, so not removed
Package 'php-doctrine-phpcr-odm' is not installed, so not removed
Package 'php-doctrine-mongodb-odm' is not installed, so not removed
Package 'php-ml-json-ld' is not installed, so not removed
Package 'php-semsol-arc2' is not installed, so not removed
Package 'php-com-dotnet' is not installed, so not removed
Package 'php-rar' is not installed, so not removed
Package 'php-laminas-httphandlerrunner' is not installed, so not removed
Package 'php-cordoval-hamcrest-php' is not installed, so not removed
Package 'php-davedevelopment-hamcrest-php' is not installed, so not removed
Package 'php-kodova-hamcrest-php' is not installed, so not removed
Package 'php-scrutinizer-ocular' is not installed, so not removed
Package 'php-league-flysystem-sftp' is not installed, so not removed
Package 'php-league-flysystem-eventable-filesystem' is not installed, so not removed
Package 'php-league-flysystem-rackspace' is not installed, so not removed
Package 'php-league-flysystem-azure' is not installed, so not removed
Package 'php-league-flysystem-webdav' is not installed, so not removed
Package 'php-league-flysystem-aws-s3-v2' is not installed, so not removed
Package 'php-league-flysystem-aws-s3-v3' is not installed, so not removed
Package 'php-spatie-flysystem-dropbox' is not installed, so not removed
Package 'php-srmklive-flysystem-dropbox-v2' is not installed, so not removed
Package 'php-league-flysystem-cached-adapter' is not installed, so not removed
Package 'php-league-flysystem-ziparchive' is not installed, so not removed
Package 'php-sqlite' is not installed, so not removed
Package 'php-lzf' is not installed, so not removed
Package 'php-pnctl' is not installed, so not removed
Package 'php-mdb2-driver-fbsql' is not installed, so not removed
Package 'php-mdb2-driver-ibase' is not installed, so not removed
Package 'php-mdb2-driver-mssql' is not installed, so not removed
Package 'php-mdb2-driver-mysqli' is not installed, so not removed
Package 'php-mdb2-driver-oci8' is not installed, so not removed
Package 'php-mdb2-driver-odbc' is not installed, so not removed
Package 'php-mdb2-driver-querysim' is not installed, so not removed
Package 'php-mdb2-driver-sqlite' is not installed, so not removed
Package 'php-mdb2-driver-sqlsrv' is not installed, so not removed
Package 'php-barnabywalters-mf-cleaner' is not installed, so not removed
Package 'php-php-mock-phpunit' is not installed, so not removed
Package 'php-graylog2-gelf-php' is not installed, so not removed
Package 'php-doctrine-couchdb' is not installed, so not removed
Package 'php-ruflin-elastica' is not installed, so not removed
Package 'php-elasticsearch' is not installed, so not removed
Package 'php-aws-sdk' is not installed, so not removed
Package 'php-rollbar' is not installed, so not removed
Package 'php-console' is not installed, so not removed
Package 'php-wfio' is not installed, so not removed
Package 'php-dbase' is not installed, so not removed
Package 'php-libsodium' is not installed, so not removed
Package 'libphp-predis' is not installed, so not removed
Package 'php-phpiredis' is not installed, so not removed
Package 'php-ocramius-proxy-manager' is not installed, so not removed
Package 'php-zendframework-zend-stdlib' is not installed, so not removed
Package 'php-rhumsaa-uuid' is not installed, so not removed
Package 'php-moontoast-math' is not installed, so not removed
Package 'php-ramsey-uuid-console' is not installed, so not removed
Package 'php-ramsey-uuid-doctrine' is not installed, so not removed
Package 'php-paragonie-random-lib' is not installed, so not removed
Package 'php-crypt-blowfish' is not installed, so not removed
Package 'php-compat' is not installed, so not removed
Package 'php-cache' is not installed, so not removed
Package 'php-xml-serializer' is not installed, so not removed
Package 'libssh2-php' is not installed, so not removed
Package 'php-enqueue-messenger-adapter' is not installed, so not removed
Package 'php-numbers-words' is not installed, so not removed
Package 'php7.0-thrift' is not installed, so not removed
Package 'php7.2-thrift' is not installed, so not removed
Package 'php-net-idna' is not installed, so not removed
Package 'php-phpstan' is not installed, so not removed
Package 'php-vimeo-psalm' is not installed, so not removed
Package 'php-zendframework-zend-eventmanager' is not installed, so not removed
Package 'php-container-interop' is not installed, so not removed
Package 'php-recode' is not installed, so not removed
Package 'php-gd2' is not installed, so not removed
Package 'php-pragmarx-google2fa-qrcode' is not installed, so not removed
Package 'php-samyoul-u2f-php-server' is not installed, so not removed
Package 'php5-xsl' is not installed, so not removed
Package 'php5-imagick' is not installed, so not removed
Package 'php-endroid-qr-code' is not installed, so not removed
Package 'php-guzzlehttp-guzzle' is not installed, so not removed
Package 'php-bjeavons-zxcvbn-php' is not installed, so not removed
Package 'php7.4-fpm' is not installed, so not removed
Package 'libapache2-mod-php' is not installed, so not removed
Package 'php' is not installed, so not removed
Package 'php-all-dev' is not installed, so not removed
Package 'php-cgi' is not installed, so not removed
Package 'php-cli' is not installed, so not removed
Package 'php-curl' is not installed, so not removed
Package 'php-dev' is not installed, so not removed
Package 'php-gd' is not installed, so not removed
Package 'php-gmp' is not installed, so not removed
Package 'php-json' is not installed, so not removed
Package 'php-ldap' is not installed, so not removed
Package 'php-mysql' is not installed, so not removed
Package 'php-odbc' is not installed, so not removed
Package 'php-pgsql' is not installed, so not removed
Package 'php-pspell' is not installed, so not removed
Package 'php-snmp' is not installed, so not removed
Package 'php-sqlite3' is not installed, so not removed
Package 'php-tidy' is not installed, so not removed
Package 'php-xml' is not installed, so not removed
Package 'pkg-php-tools' is not installed, so not removed
Package 'cakephp' is not installed, so not removed
Package 'cakephp-scripts' is not installed, so not removed
Package 'dh-php' is not installed, so not removed
Package 'elpa-php-mode' is not installed, so not removed
Package 'golang-github-phpdave11-gofpdi-dev' is not installed, so not removed
Package 'gosa-plugin-phpgw' is not installed, so not removed
Package 'gosa-plugin-phpgw-schema' is not installed, so not removed
Package 'gosa-plugin-phpscheduleit' is not installed, so not removed
Package 'gosa-plugin-phpscheduleit-schema' is not installed, so not removed
Package 'icinga-php-library' is not installed, so not removed
Package 'icinga-php-thirdparty' is not installed, so not removed
Package 'kdevelop-php' is not installed, so not removed
Package 'kdevelop-php-l10n' is not installed, so not removed
Package 'libawl-php' is not installed, so not removed
Package 'libhtml-wikiconverter-phpwiki-perl' is not installed, so not removed
Package 'libjs-php-date-formatter' is not installed, so not removed
Package 'libmarkdown-php' is not installed, so not removed
Package 'libnusoap-php' is not installed, so not removed
Package 'libow-php7' is not installed, so not removed
Package 'libownet-php' is not installed, so not removed
Package 'libphp-adodb' is not installed, so not removed
Package 'libphp-embed' is not installed, so not removed
Package 'libphp-jabber' is not installed, so not removed
Package 'libphp-jpgraph' is not installed, so not removed
Package 'libphp-jpgraph-examples' is not installed, so not removed
Package 'libphp-phpmailer' is not installed, so not removed
Package 'libphp-serialization-perl' is not installed, so not removed
Package 'libphp-simplepie' is not installed, so not removed
Package 'libphp-snoopy' is not installed, so not removed
Package 'libphpy-dev' is not installed, so not removed
Package 'libphpy1' is not installed, so not removed
Package 'libsparkline-php' is not installed, so not removed
Package 'libxrdcephposix0' is not installed, so not removed
Package 'mlmmj-php-web' is not installed, so not removed
Package 'mlmmj-php-web-admin' is not installed, so not removed
Package 'php-amqp' is not installed, so not removed
Package 'php-amqp-all-dev' is not installed, so not removed
Package 'php-amqplib' is not installed, so not removed
Package 'php-apcu' is not installed, so not removed
Package 'php-apcu-all-dev' is not installed, so not removed
Package 'php-arthurhoaro-web-thumbnailer' is not installed, so not removed
Package 'php-ast' is not installed, so not removed
Package 'php-ast-all-dev' is not installed, so not removed
Package 'php-async-aws-core' is not installed, so not removed
Package 'php-async-aws-ses' is not installed, so not removed
Package 'php-async-aws-sns' is not installed, so not removed
Package 'php-async-aws-sqs' is not installed, so not removed
Package 'php-auth-sasl' is not installed, so not removed
Package 'php-bacon-qr-code' is not installed, so not removed
Package 'php-bcmath' is not installed, so not removed
Package 'php-bz2' is not installed, so not removed
Package 'php-cache-integration-tests' is not installed, so not removed
Package 'php-cache-lite' is not installed, so not removed
Package 'php-cache-tag-interop' is not installed, so not removed
Package 'php-cas' is not installed, so not removed
Package 'php-codecoverage' is not installed, so not removed
Package 'php-codesniffer' is not installed, so not removed
Package 'php-composer-ca-bundle' is not installed, so not removed
Package 'php-composer-metadata-minifier' is not installed, so not removed
Package 'php-composer-pcre' is not installed, so not removed
Package 'php-composer-semver' is not installed, so not removed
Package 'php-composer-spdx-licenses' is not installed, so not removed
Package 'php-composer-xdebug-handler' is not installed, so not removed
Package 'php-console-commandline' is not installed, so not removed
Package 'php-console-table' is not installed, so not removed
Package 'php-constant-time' is not installed, so not removed
Package 'php-crypt-gpg' is not installed, so not removed
Package 'php-dapphp-radius' is not installed, so not removed
Package 'php-dasprid-enum' is not installed, so not removed
Package 'php-date' is not installed, so not removed
Package 'php-db' is not installed, so not removed
Package 'php-deepcopy' is not installed, so not removed
Package 'php-directory-scanner' is not installed, so not removed
Package 'php-doctrine-annotations' is not installed, so not removed
Package 'php-doctrine-cache' is not installed, so not removed
Package 'php-doctrine-collections' is not installed, so not removed
Package 'php-doctrine-common' is not installed, so not removed
Package 'php-doctrine-data-fixtures' is not installed, so not removed
Package 'php-doctrine-dbal' is not installed, so not removed
Package 'php-doctrine-deprecations' is not installed, so not removed
Package 'php-doctrine-event-manager' is not installed, so not removed
Package 'php-doctrine-inflector' is not installed, so not removed
Package 'php-doctrine-instantiator' is not installed, so not removed
Package 'php-doctrine-lexer' is not installed, so not removed
Package 'php-doctrine-orm' is not installed, so not removed
Package 'php-doctrine-persistence' is not installed, so not removed
Package 'php-doctrine-reflection' is not installed, so not removed
Package 'php-dompdf' is not installed, so not removed
Package 'php-dragonmantank-cron-expression' is not installed, so not removed
Package 'php-ds' is not installed, so not removed
Package 'php-ds-all-dev' is not installed, so not removed
Package 'php-easyrdf' is not installed, so not removed
Package 'php-email-validator' is not installed, so not removed
Package 'php-enchant' is not installed, so not removed
Package 'php-excimer' is not installed, so not removed
Package 'php-facedetect' is not installed, so not removed
Package 'php-fdomdocument' is not installed, so not removed
Package 'php-fig-link-util' is not installed, so not removed
Package 'php-file-iterator' is not installed, so not removed
Package 'php-finder-facade' is not installed, so not removed
Package 'php-finder-facade-doc' is not installed, so not removed
Package 'php-font-lib' is not installed, so not removed
Package 'php-fpdf' is not installed, so not removed
Package 'php-fpm' is not installed, so not removed
Package 'php-fxsl' is not installed, so not removed
Package 'php-gearman' is not installed, so not removed
Package 'php-gearman-all-dev' is not installed, so not removed
Package 'php-geos' is not installed, so not removed
Package 'php-geshi' is not installed, so not removed
Package 'php-getallheaders' is not installed, so not removed
Package 'php-getid3' is not installed, so not removed
Package 'php-gettext-languages' is not installed, so not removed
Package 'php-gmagick' is not installed, so not removed
Package 'php-gmagick-all-dev' is not installed, so not removed
Package 'php-gnupg' is not installed, so not removed
Package 'php-gnupg-all-dev' is not installed, so not removed
Package 'php-google-recaptcha' is not installed, so not removed
Package 'php-guestfs' is not installed, so not removed
Package 'php-guzzlehttp-promises' is not installed, so not removed
Package 'php-guzzlehttp-psr7' is not installed, so not removed
Package 'php-hamcrest' is not installed, so not removed
Package 'php-htmlawed' is not installed, so not removed
Package 'php-htmlpurifier' is not installed, so not removed
Package 'php-http' is not installed, so not removed
Package 'php-http-all-dev' is not installed, so not removed
Package 'php-http-httplug' is not installed, so not removed
Package 'php-http-interop-http-factory-tests' is not installed, so not removed
Package 'php-http-message-factory' is not installed, so not removed
Package 'php-http-promise' is not installed, so not removed
Package 'php-http-psr7-integration-tests' is not installed, so not removed
Package 'php-http-request' is not installed, so not removed
Package 'php-http-request2' is not installed, so not removed
Package 'php-http-webdav-server' is not installed, so not removed
Package 'php-httpful' is not installed, so not removed
Package 'php-icinga' is not installed, so not removed
Package 'php-igbinary' is not installed, so not removed
Package 'php-igbinary-all-dev' is not installed, so not removed
Package 'php-image-text' is not installed, so not removed
Package 'php-imagick' is not installed, so not removed
Package 'php-imagick-all-dev' is not installed, so not removed
Package 'php-imap' is not installed, so not removed
Package 'php-interbase' is not installed, so not removed
Package 'php-intl' is not installed, so not removed
Package 'php-invoker' is not installed, so not removed
Package 'php-json-schema' is not installed, so not removed
Package 'php-klogger' is not installed, so not removed
Package 'php-league-commonmark' is not installed, so not removed
Package 'php-league-flysystem' is not installed, so not removed
Package 'php-league-html-to-markdown' is not installed, so not removed
Package 'php-league-mime-type-detection' is not installed, so not removed
Package 'php-letodms-core' is not installed, so not removed
Package 'php-libvirt-php' is not installed, so not removed
Package 'php-log' is not installed, so not removed
Package 'php-lorenzo-pinky' is not installed, so not removed
Package 'php-luasandbox' is not installed, so not removed
Package 'php-mail' is not installed, so not removed
Package 'php-mail-mime' is not installed, so not removed
Package 'php-mailparse' is not installed, so not removed
Package 'php-mailparse-all-dev' is not installed, so not removed
Package 'php-malkusch-lock' is not installed, so not removed
Package 'php-mapi' is not installed, so not removed
Package 'php-mariadb-mysql-kbs' is not installed, so not removed
Package 'php-masterminds-html5' is not installed, so not removed
Package 'php-mbstring' is not installed, so not removed
Package 'php-mdb2' is not installed, so not removed
Package 'php-mdb2-driver-mysql' is not installed, so not removed
Package 'php-mdb2-driver-pgsql' is not installed, so not removed
Package 'php-memcache' is not installed, so not removed
Package 'php-memcache-all-dev' is not installed, so not removed
Package 'php-memcached' is not installed, so not removed
Package 'php-memcached-all-dev' is not installed, so not removed
Package 'php-mf2' is not installed, so not removed
Package 'php-mikey179-vfsstream' is not installed, so not removed
Package 'php-mime-type' is not installed, so not removed
Package 'php-mock' is not installed, so not removed
Package 'php-mock-integration' is not installed, so not removed
Package 'php-mock-phpunit' is not installed, so not removed
Package 'php-mockery' is not installed, so not removed
Package 'php-mockery-doc' is not installed, so not removed
Package 'php-mongodb' is not installed, so not removed
Package 'php-mongodb-all-dev' is not installed, so not removed
Package 'php-monolog' is not installed, so not removed
Package 'php-msgpack' is not installed, so not removed
Package 'php-msgpack-all-dev' is not installed, so not removed
Package 'php-net-dime' is not installed, so not removed
Package 'php-net-dns2' is not installed, so not removed
Package 'php-net-ftp' is not installed, so not removed
Package 'php-net-imap' is not installed, so not removed
Package 'php-net-ipv6' is not installed, so not removed
Package 'php-net-ldap2' is not installed, so not removed
Package 'php-net-ldap3' is not installed, so not removed
Package 'php-net-nntp' is not installed, so not removed
Package 'php-net-publicsuffix' is not installed, so not removed
Package 'php-net-sieve' is not installed, so not removed
Package 'php-net-smtp' is not installed, so not removed
Package 'php-net-socket' is not installed, so not removed
Package 'php-net-url' is not installed, so not removed
Package 'php-net-url2' is not installed, so not removed
Package 'php-net-whois' is not installed, so not removed
Package 'php-netscape-bookmark-parser' is not installed, so not removed
Package 'php-nikic-fast-route' is not installed, so not removed
Package 'php-nrk-predis' is not installed, so not removed
Package 'php-nyholm-psr7' is not installed, so not removed
Package 'php-oauth' is not installed, so not removed
Package 'php-oauth-all-dev' is not installed, so not removed
Package 'php-opis-closure' is not installed, so not removed
Package 'php-parsedown' is not installed, so not removed
Package 'php-parsedown-extra' is not installed, so not removed
Package 'php-parser' is not installed, so not removed
Package 'php-patchwork-utf8' is not installed, so not removed
Package 'php-pclzip' is not installed, so not removed
Package 'php-pcov' is not installed, so not removed
Package 'php-pcov-all-dev' is not installed, so not removed
Package 'php-pda-pheanstalk' is not installed, so not removed
Package 'php-phar-io-manifest' is not installed, so not removed
Package 'php-phar-io-version' is not installed, so not removed
Package 'php-php-gettext' is not installed, so not removed
Package 'php-phpdbg' is not installed, so not removed
Package 'php-phpdocumentor-reflection-common' is not installed, so not removed
Package 'php-phpdocumentor-reflection-docblock' is not installed, so not removed
Package 'php-phpdocumentor-type-resolver' is not installed, so not removed
Package 'php-phpmyadmin-motranslator' is not installed, so not removed
Package 'php-phpmyadmin-shapefile' is not installed, so not removed
Package 'php-phpmyadmin-sql-parser' is not installed, so not removed
Package 'php-phpoption' is not installed, so not removed
Package 'php-phpseclib' is not installed, so not removed
Package 'php-phpseclib3' is not installed, so not removed
Package 'php-phpspec-prophecy' is not installed, so not removed
Package 'php-phpspec-prophecy-phpunit' is not installed, so not removed
Package 'php-phpstan-phpdoc-parser' is not installed, so not removed
Package 'php-predis' is not installed, so not removed
Package 'php-proxy-manager' is not installed, so not removed
Package 'php-ps' is not installed, so not removed
Package 'php-ps-all-dev' is not installed, so not removed
Package 'php-psr' is not installed, so not removed
Package 'php-psr-all-dev' is not installed, so not removed
Package 'php-psr-cache' is not installed, so not removed
Package 'php-psr-container' is not installed, so not removed
Package 'php-psr-event-dispatcher' is not installed, so not removed
Package 'php-psr-http-client' is not installed, so not removed
Package 'php-psr-http-factory' is not installed, so not removed
Package 'php-psr-http-message' is not installed, so not removed
Package 'php-psr-link' is not installed, so not removed
Package 'php-psr-log' is not installed, so not removed
Package 'php-psr-simple-cache' is not installed, so not removed
Package 'php-pubsubhubbub-publisher' is not installed, so not removed
Package 'php-ramsey-uuid' is not installed, so not removed
Package 'php-random-compat' is not installed, so not removed
Package 'php-raphf' is not installed, so not removed
Package 'php-raphf-all-dev' is not installed, so not removed
Package 'php-react-promise' is not installed, so not removed
Package 'php-readline' is not installed, so not removed
Package 'php-redis' is not installed, so not removed
Package 'php-redis-all-dev' is not installed, so not removed
Package 'php-remctl' is not installed, so not removed
Package 'php-roundcube-rtf-html-php' is not installed, so not removed
Package 'php-rrd' is not installed, so not removed
Package 'php-rrd-all-dev' is not installed, so not removed
Package 'php-sabre-dav' is not installed, so not removed
Package 'php-sabre-vobject' is not installed, so not removed
Package 'php-sass' is not installed, so not removed
Package 'php-seclib' is not installed, so not removed
Package 'php-services-json' is not installed, so not removed
Package 'php-services-weather' is not installed, so not removed
Package 'php-shellcommand' is not installed, so not removed
Package 'php-smbclient' is not installed, so not removed
Package 'php-smbclient-all-dev' is not installed, so not removed
Package 'php-soap' is not installed, so not removed
Package 'php-solr' is not installed, so not removed
Package 'php-solr-all-dev' is not installed, so not removed
Package 'php-sql-formatter' is not installed, so not removed
Package 'php-ssh2' is not installed, so not removed
Package 'php-ssh2-all-dev' is not installed, so not removed
Package 'php-swiftmailer' is not installed, so not removed
Package 'php-sybase' is not installed, so not removed
Package 'php-symfony' is not installed, so not removed
Package 'php-symfony-all-my-sms-notifier' is not installed, so not removed
Package 'php-symfony-amazon-mailer' is not installed, so not removed
Package 'php-symfony-amazon-sns-notifier' is not installed, so not removed
Package 'php-symfony-amazon-sqs-messenger' is not installed, so not removed
Package 'php-symfony-amqp-messenger' is not installed, so not removed
Package 'php-symfony-asset' is not installed, so not removed
Package 'php-symfony-beanstalkd-messenger' is not installed, so not removed
Package 'php-symfony-browser-kit' is not installed, so not removed
Package 'php-symfony-cache' is not installed, so not removed
Package 'php-symfony-cache-contracts' is not installed, so not removed
Package 'php-symfony-clickatell-notifier' is not installed, so not removed
Package 'php-symfony-config' is not installed, so not removed
Package 'php-symfony-console' is not installed, so not removed
Package 'php-symfony-contracts' is not installed, so not removed
Package 'php-symfony-crowdin-translation-provider' is not installed, so not removed
Package 'php-symfony-css-selector' is not installed, so not removed
Package 'php-symfony-debug-bundle' is not installed, so not removed
Package 'php-symfony-dependency-injection' is not installed, so not removed
Package 'php-symfony-deprecation-contracts' is not installed, so not removed
Package 'php-symfony-discord-notifier' is not installed, so not removed
Package 'php-symfony-doctrine-bridge' is not installed, so not removed
Package 'php-symfony-doctrine-messenger' is not installed, so not removed
Package 'php-symfony-dom-crawler' is not installed, so not removed
Package 'php-symfony-dotenv' is not installed, so not removed
Package 'php-symfony-error-handler' is not installed, so not removed
Package 'php-symfony-esendex-notifier' is not installed, so not removed
Package 'php-symfony-event-dispatcher' is not installed, so not removed
Package 'php-symfony-event-dispatcher-contracts' is not installed, so not removed
Package 'php-symfony-expo-notifier' is not installed, so not removed
Package 'php-symfony-expression-language' is not installed, so not removed
Package 'php-symfony-fake-chat-notifier' is not installed, so not removed
Package 'php-symfony-fake-sms-notifier' is not installed, so not removed
Package 'php-symfony-filesystem' is not installed, so not removed
Package 'php-symfony-finder' is not installed, so not removed
Package 'php-symfony-firebase-notifier' is not installed, so not removed
Package 'php-symfony-form' is not installed, so not removed
Package 'php-symfony-framework-bundle' is not installed, so not removed
Package 'php-symfony-free-mobile-notifier' is not installed, so not removed
Package 'php-symfony-gateway-api-notifier' is not installed, so not removed
Package 'php-symfony-gitter-notifier' is not installed, so not removed
Package 'php-symfony-google-chat-notifier' is not installed, so not removed
Package 'php-symfony-google-mailer' is not installed, so not removed
Package 'php-symfony-http-client' is not installed, so not removed
Package 'php-symfony-http-client-contracts' is not installed, so not removed
Package 'php-symfony-http-foundation' is not installed, so not removed
Package 'php-symfony-http-kernel' is not installed, so not removed
Package 'php-symfony-inflector' is not installed, so not removed
Package 'php-symfony-infobip-notifier' is not installed, so not removed
Package 'php-symfony-intl' is not installed, so not removed
Package 'php-symfony-iqsms-notifier' is not installed, so not removed
Package 'php-symfony-ldap' is not installed, so not removed
Package 'php-symfony-light-sms-notifier' is not installed, so not removed
Package 'php-symfony-linked-in-notifier' is not installed, so not removed
Package 'php-symfony-lock' is not installed, so not removed
Package 'php-symfony-loco-translation-provider' is not installed, so not removed
Package 'php-symfony-lokalise-translation-provider' is not installed, so not removed
Package 'php-symfony-mailchimp-mailer' is not installed, so not removed
Package 'php-symfony-mailer' is not installed, so not removed
Package 'php-symfony-mailgun-mailer' is not installed, so not removed
Package 'php-symfony-mailjet-mailer' is not installed, so not removed
Package 'php-symfony-mailjet-notifier' is not installed, so not removed
Package 'php-symfony-mattermost-notifier' is not installed, so not removed
Package 'php-symfony-mercure' is not installed, so not removed
Package 'php-symfony-mercure-notifier' is not installed, so not removed
Package 'php-symfony-message-bird-notifier' is not installed, so not removed
Package 'php-symfony-message-media-notifier' is not installed, so not removed
Package 'php-symfony-messenger' is not installed, so not removed
Package 'php-symfony-microsoft-teams-notifier' is not installed, so not removed
Package 'php-symfony-mime' is not installed, so not removed
Package 'php-symfony-mobyt-notifier' is not installed, so not removed
Package 'php-symfony-monolog-bridge' is not installed, so not removed
Package 'php-symfony-nexmo-notifier' is not installed, so not removed
Package 'php-symfony-notifier' is not installed, so not removed
Package 'php-symfony-octopush-notifier' is not installed, so not removed
Package 'php-symfony-oh-my-smtp-mailer' is not installed, so not removed
Package 'php-symfony-one-signal-notifier' is not installed, so not removed
Package 'php-symfony-options-resolver' is not installed, so not removed
Package 'php-symfony-ovh-cloud-notifier' is not installed, so not removed
Package 'php-symfony-password-hasher' is not installed, so not removed
Package 'php-symfony-phpunit-bridge' is not installed, so not removed
Package 'php-symfony-polyfill' is not installed, so not removed
Package 'php-symfony-polyfill-apcu' is not installed, so not removed
Package 'php-symfony-polyfill-ctype' is not installed, so not removed
Package 'php-symfony-polyfill-iconv' is not installed, so not removed
Package 'php-symfony-polyfill-intl-grapheme' is not installed, so not removed
Package 'php-symfony-polyfill-intl-icu' is not installed, so not removed
Package 'php-symfony-polyfill-intl-idn' is not installed, so not removed
Package 'php-symfony-polyfill-intl-messageformatter' is not installed, so not removed
Package 'php-symfony-polyfill-intl-normalizer' is not installed, so not removed
Package 'php-symfony-polyfill-mbstring' is not installed, so not removed
Package 'php-symfony-polyfill-php72' is not installed, so not removed
Package 'php-symfony-polyfill-php73' is not installed, so not removed
Package 'php-symfony-polyfill-php74' is not installed, so not removed
Package 'php-symfony-polyfill-php80' is not installed, so not removed
Package 'php-symfony-polyfill-php81' is not installed, so not removed
Package 'php-symfony-polyfill-util' is not installed, so not removed
Package 'php-symfony-polyfill-uuid' is not installed, so not removed
Package 'php-symfony-polyfill-xml' is not installed, so not removed
Package 'php-symfony-postmark-mailer' is not installed, so not removed
Package 'php-symfony-process' is not installed, so not removed
Package 'php-symfony-property-access' is not installed, so not removed
Package 'php-symfony-property-info' is not installed, so not removed
Package 'php-symfony-proxy-manager-bridge' is not installed, so not removed
Package 'php-symfony-rate-limiter' is not installed, so not removed
Package 'php-symfony-redis-messenger' is not installed, so not removed
Package 'php-symfony-rocket-chat-notifier' is not installed, so not removed
Package 'php-symfony-routing' is not installed, so not removed
Package 'php-symfony-runtime' is not installed, so not removed
Package 'php-symfony-security-acl' is not installed, so not removed
Package 'php-symfony-security-bundle' is not installed, so not removed
Package 'php-symfony-security-core' is not installed, so not removed
Package 'php-symfony-security-csrf' is not installed, so not removed
Package 'php-symfony-security-guard' is not installed, so not removed
Package 'php-symfony-security-http' is not installed, so not removed
Package 'php-symfony-semaphore' is not installed, so not removed
Package 'php-symfony-sendgrid-mailer' is not installed, so not removed
Package 'php-symfony-sendinblue-mailer' is not installed, so not removed
Package 'php-symfony-sendinblue-notifier' is not installed, so not removed
Package 'php-symfony-serializer' is not installed, so not removed
Package 'php-symfony-service-contracts' is not installed, so not removed
Package 'php-symfony-sinch-notifier' is not installed, so not removed
Package 'php-symfony-slack-notifier' is not installed, so not removed
Package 'php-symfony-sms-biuras-notifier' is not installed, so not removed
Package 'php-symfony-sms77-notifier' is not installed, so not removed
Package 'php-symfony-smsapi-notifier' is not installed, so not removed
Package 'php-symfony-smsc-notifier' is not installed, so not removed
Package 'php-symfony-spot-hit-notifier' is not installed, so not removed
Package 'php-symfony-stopwatch' is not installed, so not removed
Package 'php-symfony-string' is not installed, so not removed
Package 'php-symfony-telegram-notifier' is not installed, so not removed
Package 'php-symfony-telnyx-notifier' is not installed, so not removed
Package 'php-symfony-templating' is not installed, so not removed
Package 'php-symfony-translation' is not installed, so not removed
Package 'php-symfony-translation-contracts' is not installed, so not removed
Package 'php-symfony-turbo-sms-notifier' is not installed, so not removed
Package 'php-symfony-twig-bridge' is not installed, so not removed
Package 'php-symfony-twig-bundle' is not installed, so not removed
Package 'php-symfony-twilio-notifier' is not installed, so not removed
Package 'php-symfony-uid' is not installed, so not removed
Package 'php-symfony-validator' is not installed, so not removed
Package 'php-symfony-var-dumper' is not installed, so not removed
Package 'php-symfony-var-exporter' is not installed, so not removed
Package 'php-symfony-vonage-notifier' is not installed, so not removed
Package 'php-symfony-web-link' is not installed, so not removed
Package 'php-symfony-web-profiler-bundle' is not installed, so not removed
Package 'php-symfony-workflow' is not installed, so not removed
Package 'php-symfony-yaml' is not installed, so not removed
Package 'php-symfony-yunpian-notifier' is not installed, so not removed
Package 'php-symfony-zulip-notifier' is not installed, so not removed
Package 'php-tcpdf' is not installed, so not removed
Package 'php-text-captcha' is not installed, so not removed
Package 'php-text-figlet' is not installed, so not removed
Package 'php-text-languagedetect' is not installed, so not removed
Package 'php-text-password' is not installed, so not removed
Package 'php-text-template' is not installed, so not removed
Package 'php-text-wiki' is not installed, so not removed
Package 'php-thrift' is not installed, so not removed
Package 'php-tideways' is not installed, so not removed
Package 'php-tijsverkoyen-css-to-inline-styles' is not installed, so not removed
Package 'php-timer' is not installed, so not removed
Package 'php-token-stream' is not installed, so not removed
Package 'php-tokenizer' is not installed, so not removed
Package 'php-twig' is not installed, so not removed
Package 'php-twig-cache-extra' is not installed, so not removed
Package 'php-twig-cssinliner-extra' is not installed, so not removed
Package 'php-twig-doc' is not installed, so not removed
Package 'php-twig-extra-bundle' is not installed, so not removed
Package 'php-twig-html-extra' is not installed, so not removed
Package 'php-twig-i18n-extension' is not installed, so not removed
Package 'php-twig-inky-extra' is not installed, so not removed
Package 'php-twig-intl-extra' is not installed, so not removed
Package 'php-twig-markdown-extra' is not installed, so not removed
Package 'php-twig-string-extra' is not installed, so not removed
Package 'php-uopz' is not installed, so not removed
Package 'php-uopz-all-dev' is not installed, so not removed
Package 'php-uploadprogress' is not installed, so not removed
Package 'php-uploadprogress-all-dev' is not installed, so not removed
Package 'php-uuid' is not installed, so not removed
Package 'php-uuid-all-dev' is not installed, so not removed
Package 'php-validate' is not installed, so not removed
Package 'php-vlucas-phpdotenv' is not installed, so not removed
Package 'php-webmozart-assert' is not installed, so not removed
Package 'php-wikidiff2' is not installed, so not removed
Package 'php-xajax' is not installed, so not removed
Package 'php-xdebug' is not installed, so not removed
Package 'php-xdebug-all-dev' is not installed, so not removed
Package 'php-xml-htmlsax3' is not installed, so not removed
Package 'php-xml-rpc2' is not installed, so not removed
Package 'php-xml-svg' is not installed, so not removed
Package 'php-xmlrpc' is not installed, so not removed
Package 'php-xmlrpc-all-dev' is not installed, so not removed
Package 'php-yac' is not installed, so not removed
Package 'php-yac-all-dev' is not installed, so not removed
Package 'php-yaml' is not installed, so not removed
Package 'php-yaml-all-dev' is not installed, so not removed
Package 'php-zend-code' is not installed, so not removed
Package 'php-zend-eventmanager' is not installed, so not removed
Package 'php-zend-stdlib' is not installed, so not removed
Package 'php-zeroc-ice' is not installed, so not removed
Package 'php-zeta-base' is not installed, so not removed
Package 'php-zeta-console-tools' is not installed, so not removed
Package 'php-zeta-unit-test' is not installed, so not removed
Package 'php-zip' is not installed, so not removed
Package 'php-zmq' is not installed, so not removed
Package 'php-zmq-all-dev' is not installed, so not removed
Package 'php8.1-ast' is not installed, so not removed
Package 'php8.1-ds' is not installed, so not removed
Package 'php8.1-gearman' is not installed, so not removed
Package 'php8.1-gmagick' is not installed, so not removed
Package 'php8.1-gnupg' is not installed, so not removed
Package 'php8.1-http' is not installed, so not removed
Package 'php8.1-mailparse' is not installed, so not removed
Package 'php8.1-oauth' is not installed, so not removed
Package 'php8.1-ps' is not installed, so not removed
Package 'php8.1-psr' is not installed, so not removed
Package 'php8.1-raphf' is not installed, so not removed
Package 'php8.1-rrd' is not installed, so not removed
Package 'php8.1-smbclient' is not installed, so not removed
Package 'php8.1-solr' is not installed, so not removed
Package 'php8.1-ssh2' is not installed, so not removed
Package 'php8.1-uopz' is not installed, so not removed
Package 'php8.1-uploadprogress' is not installed, so not removed
Package 'php8.1-uuid' is not installed, so not removed
Package 'php8.1-xmlrpc' is not installed, so not removed
Package 'php8.1-yac' is not installed, so not removed
Package 'phpab' is not installed, so not removed
Package 'phpcpd' is not installed, so not removed
Package 'phpdox' is not installed, so not removed
Package 'phpldapadmin' is not installed, so not removed
Package 'phpliteadmin' is not installed, so not removed
Package 'phpliteadmin-themes' is not installed, so not removed
Package 'phploc' is not installed, so not removed
Package 'phpmd' is not installed, so not removed
Package 'phpmyadmin' is not installed, so not removed
Package 'phppgadmin' is not installed, so not removed
Package 'phpqrcode' is not installed, so not removed
Package 'phpsysinfo' is not installed, so not removed
Package 'phpunit' is not installed, so not removed
Package 'phpunit-cli-parser' is not installed, so not removed
Package 'phpunit-code-unit' is not installed, so not removed
Package 'phpunit-code-unit-reverse-lookup' is not installed, so not removed
Package 'phpunit-comparator' is not installed, so not removed
Package 'phpunit-complexity' is not installed, so not removed
Package 'phpunit-diff' is not installed, so not removed
Package 'phpunit-environment' is not installed, so not removed
Package 'phpunit-exporter' is not installed, so not removed
Package 'phpunit-git' is not installed, so not removed
Package 'phpunit-global-state' is not installed, so not removed
Package 'phpunit-lines-of-code' is not installed, so not removed
Package 'phpunit-object-enumerator' is not installed, so not removed
Package 'phpunit-object-reflector' is not installed, so not removed
Package 'phpunit-recursion-context' is not installed, so not removed
Package 'phpunit-resource-operations' is not installed, so not removed
Package 'phpunit-type' is not installed, so not removed
Package 'phpunit-version' is not installed, so not removed
Package 'phpwebcounter' is not installed, so not removed
Package 'phpwebcounter-extra' is not installed, so not removed
Package 'python3-phply' is not installed, so not removed
Package 'python3-phpserialize' is not installed, so not removed
Package 'simplesamlphp' is not installed, so not removed
Package 'slbackup-php' is not installed, so not removed
Package 'uphpmvault' is not installed, so not removed
Package 'weechat-php' is not installed, so not removed
Package 'zabbix-frontend-php' is not installed, so not removed
Package 'php-mythtv' is not installed, so not removed
Package 'libapache2-mod-php8.1' is not installed, so not removed
Package 'libapache2-mod-php7.4' is not installed, so not removed
Package 'libapache2-mod-php8.0' is not installed, so not removed
Package 'libphp8.1-embed' is not installed, so not removed
The following packages will be REMOVED:
  php-common php-pear php8.1 php8.1-amqp php8.1-apcu php8.1-bcmath php8.1-bz2
  php8.1-cgi php8.1-cli php8.1-common php8.1-curl php8.1-dba php8.1-dev
  php8.1-enchant php8.1-fpm php8.1-gd php8.1-gmp php8.1-igbinary
  php8.1-imagick php8.1-imap php8.1-interbase php8.1-intl php8.1-ldap
  php8.1-mbstring php8.1-memcache php8.1-memcached php8.1-mongodb
  php8.1-msgpack php8.1-mysql php8.1-odbc php8.1-opcache php8.1-pcov
  php8.1-pgsql php8.1-phpdbg php8.1-pspell php8.1-readline php8.1-redis
  php8.1-snmp php8.1-soap php8.1-sqlite3 php8.1-sybase php8.1-tidy
  php8.1-xdebug php8.1-xml php8.1-xsl php8.1-yaml php8.1-zip php8.1-zmq
0 upgraded, 0 newly installed, 48 to remove and 21 not upgraded.
After this operation, 56.6 MB disk space will be freed.
(Reading database ...
(Reading database ... 5%
(Reading database ... 10%
(Reading database ... 15%
(Reading database ... 20%
(Reading database ... 25%
(Reading database ... 30%
(Reading database ... 35%
(Reading database ... 40%
(Reading database ... 45%
(Reading database ... 50%
(Reading database ... 55%
(Reading database ... 60%
(Reading database ... 65%
(Reading database ... 70%
(Reading database ... 75%
(Reading database ... 80%
(Reading database ... 85%
(Reading database ... 90%
(Reading database ... 95%
(Reading database ... 100%
(Reading database ... 204495 files and directories currently installed.)
Removing php8.1-bcmath (8.1.2-1ubuntu2.11) ...
Removing php8.1-zip (8.1.2-1ubuntu2.11) ...
Removing php-pear (1:1.10.12+submodules+notgz+20210212-1ubuntu3) ...
Removing php8.1 (8.1.2-1ubuntu2.11) ...
Removing php8.1-amqp (1.11.0-4) ...
Removing php8.1-apcu (5.1.21+4.0.11-7ubuntu1) ...
Removing php8.1-bz2 (8.1.2-1ubuntu2.11) ...
Removing php8.1-zmq (1.1.3-23) ...
Removing php8.1-yaml (2.2.2+2.1.0+2.0.4+1.3.2-5) ...
Removing php8.1-cgi (8.1.2-1ubuntu2.11) ...
apache2_invoke php8.1-cgi prerm: No action required
Removing php8.1-dev (8.1.2-1ubuntu2.11) ...
Removing php8.1-xdebug (3.1.2+2.9.8+2.8.1+2.5.5-4) ...
Removing php8.1-phpdbg (8.1.2-1ubuntu2.11) ...
Removing php8.1-redis (5.3.5+4.3.0-5.1) ...
Removing php8.1-xsl (8.1.2-1ubuntu2.11) ...
Removing php8.1-curl (8.1.2-1ubuntu2.11) ...
Removing php8.1-dba (8.1.2-1ubuntu2.11) ...
Removing php8.1-enchant (8.1.2-1ubuntu2.11) ...
Removing php8.1-pcov (1.0.11-4) ...
Removing php8.1-fpm (8.1.2-1ubuntu2.11) ...
apache2_invoke php8.1-fpm prerm: No action required
Removing php8.1-gd (8.1.2-1ubuntu2.11) ...
Removing php8.1-gmp (8.1.2-1ubuntu2.11) ...
Removing php8.1-memcached (3.1.5+2.2.0-14.1) ...
Removing php8.1-igbinary (3.2.6+2.0.8-7ubuntu1) ...
Removing php8.1-imagick (3.6.0-4ubuntu1) ...
Removing php8.1-imap (8.1.2-1ubuntu2.11) ...
Removing php8.1-interbase (8.1.2-1ubuntu2.11) ...
Removing php8.1-intl (8.1.2-1ubuntu2.11) ...
Removing php8.1-ldap (8.1.2-1ubuntu2.11) ...
Removing php8.1-mbstring (8.1.2-1ubuntu2.11) ...
Removing php8.1-memcache (8.0+4.0.5.2+3.0.9~20170802.e702b5f9+-7) ...
Removing php8.1-mongodb (1.12.0+1.9.2+1.7.5-4) ...
Removing php8.1-msgpack (2.2.0~rc1+2.1.2+0.5.7-6) ...
Removing php8.1-mysql (8.1.2-1ubuntu2.11) ...
Removing php8.1-odbc (8.1.2-1ubuntu2.11) ...
Removing php8.1-pgsql (8.1.2-1ubuntu2.11) ...
Removing php8.1-pspell (8.1.2-1ubuntu2.11) ...
Removing php8.1-snmp (8.1.2-1ubuntu2.11) ...
Removing php8.1-soap (8.1.2-1ubuntu2.11) ...
Removing php8.1-sqlite3 (8.1.2-1ubuntu2.11) ...
Removing php8.1-sybase (8.1.2-1ubuntu2.11) ...
Removing php8.1-tidy (8.1.2-1ubuntu2.11) ...
Removing php8.1-xml (8.1.2-1ubuntu2.11) ...
Removing php8.1-cli (8.1.2-1ubuntu2.11) ...
Removing php8.1-opcache (8.1.2-1ubuntu2.11) ...
Removing php8.1-readline (8.1.2-1ubuntu2.11) ...
Removing php8.1-common (8.1.2-1ubuntu2.11) ...
Removing php-common (2:92ubuntu1) ...
Warning: Stopping phpsessionclean.service, but it can still be activated by:
  phpsessionclean.timer
Processing triggers for man-db (2.10.2-1) ...
Reading package lists...
Building dependency tree...
Reading state information...
Package 'mongodb-dev' is not installed, so not removed
Package 'mongodb-server' is not installed, so not removed
0 upgraded, 0 newly installed, 0 to remove and 21 not upgraded.
Reading package lists...
Building dependency tree...
Reading state information...
Package 'mysql-client-5.7' is not installed, so not removed
Package 'mysql-client-core-5.7' is not installed, so not removed
Package 'mysql-server-5.5' is not installed, so not removed
Package 'mysql-server-5.7' is not installed, so not removed
Package 'mysql-server-core-5.7' is not installed, so not removed
Package 'mysql-client-core-5.5' is not installed, so not removed
Package 'mysql-client-core-5.6' is not installed, so not removed
Package 'mysql-client-5.5' is not installed, so not removed
Package 'mysql-client-5.6' is not installed, so not removed
Package 'mysql-server-core-5.5' is not installed, so not removed
Package 'mysql-server-core-5.6' is not installed, so not removed
Package 'mysql-server-5.6' is not installed, so not removed
Package 'mysql-testsuite-5.5' is not installed, so not removed
Package 'mysql-testsuite-5.6' is not installed, so not removed
Package 'mysql-testsuite-5.7' is not installed, so not removed
Package 'mysql-sandbox' is not installed, so not removed
Package 'mysql-router' is not installed, so not removed
Package 'mysql-source-8.0' is not installed, so not removed
Package 'mysql-testsuite' is not installed, so not removed
Package 'mysql-testsuite-8.0' is not installed, so not removed
The following packages will be REMOVED:
  libmysqlclient-dev libmysqlclient21 mysql-client mysql-client-8.0
  mysql-client-core-8.0 mysql-common mysql-server mysql-server-8.0
  mysql-server-core-8.0 sphinxsearch
0 upgraded, 0 newly installed, 10 to remove and 21 not upgraded.
After this operation, 209 MB disk space will be freed.
(Reading database ...
(Reading database ... 5%
(Reading database ... 10%
(Reading database ... 15%
(Reading database ... 20%
(Reading database ... 25%
(Reading database ... 30%
(Reading database ... 35%
(Reading database ... 40%
(Reading database ... 45%
(Reading database ... 50%
(Reading database ... 55%
(Reading database ... 60%
(Reading database ... 65%
(Reading database ... 70%
(Reading database ... 75%
(Reading database ... 80%
(Reading database ... 85%
(Reading database ... 90%
(Reading database ... 95%
(Reading database ... 100%
(Reading database ... 203508 files and directories currently installed.)
Removing libmysqlclient-dev (8.0.33-0ubuntu0.22.04.2) ...
Removing sphinxsearch (2.2.11-8) ...
Removing libmysqlclient21:amd64 (8.0.33-0ubuntu0.22.04.2) ...
Removing mysql-client (8.0.33-0ubuntu0.22.04.2) ...
Removing mysql-server (8.0.33-0ubuntu0.22.04.2) ...
Removing mysql-server-8.0 (8.0.33-0ubuntu0.22.04.2) ...
update-alternatives: using /etc/mysql/my.cnf.fallback to provide /etc/mysql/my.cnf (my.cnf) in auto mode
Removing mysql-client-8.0 (8.0.33-0ubuntu0.22.04.2) ...
Removing mysql-client-core-8.0 (8.0.33-0ubuntu0.22.04.2) ...
Removing mysql-common (5.8+1.0.8) ...
Removing mysql-server-core-8.0 (8.0.33-0ubuntu0.22.04.2) ...
Processing triggers for man-db (2.10.2-1) ...
Processing triggers for libc-bin (2.35-0ubuntu3.1) ...
Reading package lists...
Building dependency tree...
Reading state information...
The following packages will be REMOVED:
  azure-cli firefox google-chrome-stable google-cloud-sdk libgl1
  libgl1-mesa-dri libglx-mesa0 libglx0 microsoft-edge-stable mono-complete
  mono-devel mono-roslyn mono-xsp4 mono-xsp4-base monodoc-http monodoc-manual
  powershell xvfb
0 upgraded, 0 newly installed, 18 to remove and 19 not upgraded.
After this operation, 2795 MB disk space will be freed.
(Reading database ...
(Reading database ... 5%
(Reading database ... 10%
(Reading database ... 15%
(Reading database ... 20%
(Reading database ... 25%
(Reading database ... 30%
(Reading database ... 35%
(Reading database ... 40%
(Reading database ... 45%
(Reading database ... 50%
(Reading database ... 55%
(Reading database ... 60%
(Reading database ... 65%
(Reading database ... 70%
(Reading database ... 75%
(Reading database ... 80%
(Reading database ... 85%
(Reading database ... 90%
(Reading database ... 95%
(Reading database ... 100%
(Reading database ... 203241 files and directories currently installed.)
Removing azure-cli (2.49.0-1~jammy) ...
Removing firefox (114.0.1+build1-0ubuntu0.22.04.1~mt1) ...
Removing google-chrome-stable (114.0.5735.106-1) ...
Removing google-cloud-sdk (434.0.0-0) ...
Removing xvfb (2:21.1.4-2ubuntu1.7~22.04.1) ...
Removing libgl1:amd64 (1.4.0-1) ...
Removing libglx0:amd64 (1.4.0-1) ...
Removing libglx-mesa0:amd64 (22.2.5-0ubuntu0.1~22.04.1) ...
Removing libgl1-mesa-dri:amd64 (22.2.5-0ubuntu0.1~22.04.1) ...
Removing microsoft-edge-stable (114.0.1823.43-1) ...
Removing mono-complete (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing powershell (7.2.11-1.deb) ...
Removing monodoc-http (4.2-3.1) ...
Restarting mono-xsp4 (via systemctl): mono-xsp4.service.
Removing monodoc-manual (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing mono-xsp4 (4.7.1-0xamarin3+ubuntu2004b1) ...
Removing mono-xsp4-base (4.7.1-0xamarin3+ubuntu2004b1) ...
Removing mono-roslyn (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing mono-devel (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Processing triggers for libc-bin (2.35-0ubuntu3.1) ...
Processing triggers for man-db (2.10.2-1) ...
Processing triggers for mailcap (3.70+nmu1ubuntu1) ...
Processing triggers for hicolor-icon-theme (0.17-2) ...
Reading package lists...
Building dependency tree...
Reading state information...
The following packages will be REMOVED:
  aspnetcore-targeting-pack-6.0 dictionaries-common emacsen-common
  firebird3.0-common firebird3.0-common-doc freetds-common hunspell-en-us
  lib32gcc-s1 lib32stdc++6 libaspell15 libc-client2007e libcanberra0
  libcgi-fast-perl libcgi-pm-perl libclang-common-12-dev
  libclang-common-13-dev libclang-common-14-dev libclang1-12 libclang1-13
  libclang1-14 libclone-perl libdbusmenu-glib4 libdbusmenu-gtk3-4
  libdrm-amdgpu1 libdrm-nouveau2 libdrm-radeon1 libenchant-2-2
  libencode-locale-perl libevent-pthreads-2.1-7 libfbclient2 libfcgi-bin
  libfcgi-perl libfcgi0ldbl libfontenc1 libgc1 libglapi-mesa libglvnd0
  libhtml-parser-perl libhtml-tagset-perl libhtml-template-perl
  libhttp-date-perl libhttp-message-perl libhunspell-1.7-0 libio-html-perl
  libjs-xmlextras liblldb-12 liblldb-13 liblldb-14 libllvm15
  liblwp-mediatypes-perl libmecab2 libmemcached11 libmono-2.0-1
  libmono-2.0-dev libmono-cairo4.0-cil libmono-cecil-private-cil
  libmono-cil-dev libmono-codecontracts4.0-cil
  libmono-compilerservices-symbolwriter4.0-cil libmono-cscompmgd0.0-cil
  libmono-csharp4.0c-cil libmono-custommarshalers4.0-cil
  libmono-data-tds4.0-cil libmono-db2-1.0-cil libmono-debugger-soft4.0a-cil
  libmono-http4.0-cil libmono-management4.0-cil
  libmono-messaging-rabbitmq4.0-cil libmono-microsoft-build-engine4.0-cil
  libmono-microsoft-build-tasks-v4.0-4.0-cil libmono-microsoft-build4.0-cil
  libmono-microsoft-visualc10.0-cil
  libmono-microsoft-web-infrastructure1.0-cil libmono-oracle4.0-cil
  libmono-parallel4.0-cil libmono-peapi4.0a-cil libmono-profiler
  libmono-rabbitmq4.0-cil libmono-relaxng4.0-cil libmono-sharpzip4.84-cil
  libmono-simd4.0-cil libmono-smdiagnostics0.0-cil
  libmono-system-data-datasetextensions4.0-cil
  libmono-system-data-entity4.0-cil libmono-system-data-linq4.0-cil
  libmono-system-data-services4.0-cil libmono-system-deployment4.0-cil
  libmono-system-drawing-design4.0-cil libmono-system-dynamic4.0-cil
  libmono-system-io-compression-filesystem4.0-cil
  libmono-system-json-microsoft4.0-cil libmono-system-json4.0-cil
  libmono-system-ldap-protocols4.0-cil libmono-system-management4.0-cil
  libmono-system-net-http-formatting4.0-cil
  libmono-system-net-http-webrequest4.0-cil libmono-system-net4.0-cil
  libmono-system-numerics-vectors4.0-cil libmono-system-reactive-core2.2-cil
  libmono-system-reactive-debugger2.2-cil
  libmono-system-reactive-experimental2.2-cil
  libmono-system-reactive-interfaces2.2-cil
  libmono-system-reactive-linq2.2-cil
  libmono-system-reactive-observable-aliases0.0-cil
  libmono-system-reactive-platformservices2.2-cil
  libmono-system-reactive-providers2.2-cil
  libmono-system-reactive-runtime-remoting2.2-cil
  libmono-system-reactive-windows-forms2.2-cil
  libmono-system-reactive-windows-threading2.2-cil
  libmono-system-reflection-context4.0-cil
  libmono-system-runtime-caching4.0-cil
  libmono-system-runtime-durableinstancing4.0-cil
  libmono-system-runtime4.0-cil libmono-system-servicemodel-discovery4.0-cil
  libmono-system-servicemodel-routing4.0-cil
  libmono-system-servicemodel-web4.0-cil libmono-system-serviceprocess4.0-cil
  libmono-system-threading-tasks-dataflow4.0-cil
  libmono-system-web-abstractions4.0-cil libmono-system-web-dynamicdata4.0-cil
  libmono-system-web-extensions-design4.0-cil
  libmono-system-web-extensions4.0-cil libmono-system-web-http-selfhost4.0-cil
  libmono-system-web-http-webhost4.0-cil libmono-system-web-http4.0-cil
  libmono-system-web-mobile4.0-cil libmono-system-web-mvc3.0-cil
  libmono-system-web-razor2.0-cil libmono-system-web-regularexpressions4.0-cil
  libmono-system-web-routing4.0-cil
  libmono-system-web-webpages-deployment2.0-cil
  libmono-system-web-webpages-razor2.0-cil libmono-system-web-webpages2.0-cil
  libmono-system-windows-forms-datavisualization4.0a-cil
  libmono-system-windows4.0-cil libmono-system-workflow-activities4.0-cil
  libmono-system-workflow-componentmodel4.0-cil
  libmono-system-workflow-runtime4.0-cil
  libmono-system-xml-serialization4.0-cil libmono-tasklets4.0-cil
  libmono-webmatrix-data4.0-cil libmono-xbuild-tasks4.0-cil libmonoboehm-2.0-1
  libmonosgen-2.0-1 libmonosgen-2.0-dev libnorm1 libobjc-11-dev libobjc4
  libogg0 libpfm4 libpgm-5.3-0 libprotobuf-lite23 libqdbm14 librabbitmq4
  libre2-9 libsnappy1v5 libsybdb5 libtdb1 libtidy5deb1 libtimedate-perl
  libtinfo-dev libtommath1 libu2f-udev liburi-perl libvorbis0a libvorbisfile3
  libvulkan1 libx11-xcb1 libxaw7 libxcb-dri2-0 libxcb-dri3-0 libxcb-glx0
  libxcb-present0 libxcb-sync1 libxcb-xfixes0 libxfont2 libxmu6 libxshmfence1
  libxxf86vm1 libz3-4 libz3-dev libzip4 libzmq5 libzstd-dev mecab-ipadic
  mecab-ipadic-utf8 mecab-utils mlock mono-4.0-service mono-csharp-shell
  mono-mcs mono-utils mono-xbuild monodoc-base msbuild msbuild-libhostfxr
  msbuild-sdkresolver netstandard-targeting-pack-2.1 python3-lldb-14
  referenceassemblies-pcl shtool sound-theme-freedesktop x11-xkb-utils
  xserver-common xul-ext-ubufox
0 upgraded, 0 newly installed, 205 to remove and 15 not upgraded.
After this operation, 759 MB disk space will be freed.
(Reading database ...
(Reading database ... 5%
(Reading database ... 10%
(Reading database ... 15%
(Reading database ... 20%
(Reading database ... 25%
(Reading database ... 30%
(Reading database ... 35%
(Reading database ... 40%
(Reading database ... 45%
(Reading database ... 50%
(Reading database ... 55%
(Reading database ... 60%
(Reading database ... 65%
(Reading database ... 70%
(Reading database ... 75%
(Reading database ... 80%
(Reading database ... 85%
(Reading database ... 90%
(Reading database ... 95%
(Reading database ... 100%
(Reading database ... 124798 files and directories currently installed.)
Removing aspnetcore-targeting-pack-6.0 (6.0.113-0ubuntu1~22.04.1) ...
Removing libenchant-2-2:amd64 (2.3.2-1ubuntu2) ...
Removing hunspell-en-us (1:2020.12.07-2) ...
Removing dictionaries-common (1.28.14) ...
Removing 'diversion of /usr/share/dict/words to /usr/share/dict/words.pre-dictionaries-common by dictionaries-common'
Removing emacsen-common (3.0.4) ...
Removing libfbclient2:amd64 (3.0.8.33535.ds4-1ubuntu2) ...
Removing firebird3.0-common (3.0.8.33535.ds4-1ubuntu2) ...
Removing firebird3.0-common-doc (3.0.8.33535.ds4-1ubuntu2) ...
Removing libsybdb5:amd64 (1.3.6-1) ...
Removing freetds-common (1.3.6-1) ...
Removing libclang-common-13-dev (1:13.0.1-2ubuntu2.1) ...
Removing libclang-common-14-dev (1:14.0.0-1ubuntu1) ...
Removing libaspell15:amd64 (0.60.8-4build1) ...
Removing libc-client2007e (8:2007f~dfsg-7build1) ...
Removing libcanberra0:amd64 (0.30-10ubuntu1.22.04.1) ...
Removing libcgi-fast-perl (1:2.15-1) ...
Removing libhtml-template-perl (2.97-1.1) ...
Removing libcgi-pm-perl (4.54-1) ...
Removing libclang-common-12-dev (1:12.0.1-19ubuntu3) ...
Removing libclang1-12 (1:12.0.1-19ubuntu3) ...
Removing libclang1-13 (1:13.0.1-2ubuntu2.1) ...
Removing libclang1-14 (1:14.0.0-1ubuntu1) ...
Removing libclone-perl (0.45-1build3) ...
Removing libdbusmenu-gtk3-4:amd64 (16.04.1+18.10.20180917-0ubuntu8) ...
Removing libdbusmenu-glib4:amd64 (16.04.1+18.10.20180917-0ubuntu8) ...
Removing libdrm-amdgpu1:amd64 (2.4.113-2~ubuntu0.22.04.1) ...
Removing libdrm-nouveau2:amd64 (2.4.113-2~ubuntu0.22.04.1) ...
Removing libdrm-radeon1:amd64 (2.4.113-2~ubuntu0.22.04.1) ...
Removing libhttp-message-perl (6.36-1) ...
Removing libencode-locale-perl (1.05-1.1) ...
Removing libevent-pthreads-2.1-7:amd64 (2.1.12-stable-1build3) ...
Removing libfcgi-bin (2.4.2-2build2) ...
Removing libfcgi-perl:amd64 (0.82+ds-1build1) ...
Removing libfcgi0ldbl:amd64 (2.4.2-2build2) ...
Removing libxfont2:amd64 (1:2.0.5-1build1) ...
Removing libfontenc1:amd64 (1:1.1.4-1build3) ...
Removing libobjc-11-dev:amd64 (11.3.0-1ubuntu1~22.04.1) ...
Removing libobjc4:amd64 (12.1.0-2ubuntu1~22.04) ...
Removing libgc1:amd64 (1:8.0.6-1.1build1) ...
Removing libglapi-mesa:amd64 (22.2.5-0ubuntu0.1~22.04.1) ...
Removing libglvnd0:amd64 (1.4.0-1) ...
Removing libhtml-parser-perl:amd64 (3.76-1build2) ...
Removing libhtml-tagset-perl (3.20-4) ...
Removing libhttp-date-perl (6.05-1) ...
Removing libhunspell-1.7-0:amd64 (1.7.0-4build1) ...
Removing libio-html-perl (1.004-2) ...
Removing libjs-xmlextras (20060529-2) ...
Removing liblldb-12 (1:12.0.1-19ubuntu3) ...
Removing liblldb-13 (1:13.0.1-2ubuntu2.1) ...
Removing python3-lldb-14 (1:14.0.0-1ubuntu1) ...
Removing liblldb-14 (1:14.0.0-1ubuntu1) ...
Removing libllvm15:amd64 (1:15.0.7-0ubuntu0.22.04.1) ...
Removing liblwp-mediatypes-perl (6.04-1) ...
Removing mecab-ipadic-utf8 (2.7.0-20070801+main-3) ...
update-alternatives: using /var/lib/mecab/dic/ipadic to provide /var/lib/mecab/dic/debian (mecab-dictionary) in auto mode
Removing mecab-ipadic (2.7.0-20070801+main-3) ...
Removing mecab-utils (0.996-14build9) ...
Removing libmecab2:amd64 (0.996-14build9) ...
Removing libmemcached11:amd64 (1.0.18-4.2ubuntu4) ...
Removing libmono-2.0-1 (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-2.0-dev (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-cil-dev (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-cairo4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-codecontracts4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing monodoc-base (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-compilerservices-symbolwriter4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-cscompmgd0.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing mono-csharp-shell (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-csharp4.0c-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-custommarshalers4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-data-tds4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-db2-1.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-debugger-soft4.0a-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-http4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-management4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-messaging-rabbitmq4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing referenceassemblies-pcl (2014.04.14-1xamarin7+ubuntu2004b1) ...
Removing mono-xbuild (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-microsoft-build-tasks-v4.0-4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-microsoft-build-engine4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-microsoft-build4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-microsoft-visualc10.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-web-mvc3.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-web-webpages-razor2.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-web-webpages2.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-web-webpages-deployment2.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing msbuild (1:16.10.1+xamarinxplat.2021.05.26.14.00-0xamarin2+ubuntu2004b1) ...
Removing libmono-oracle4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-parallel4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-peapi4.0a-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-profiler (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-rabbitmq4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-relaxng4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-sharpzip4.84-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-simd4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-smdiagnostics0.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-data-datasetextensions4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-data-entity4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-web-http-webhost4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-web-http-selfhost4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-web-http4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-data-services4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-servicemodel-web4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-deployment4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-drawing-design4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-dynamic4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-io-compression-filesystem4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-json-microsoft4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-json4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-ldap-protocols4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-management4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-net-http-formatting4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-net-http-webrequest4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-net4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-numerics-vectors4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-reactive-windows-threading2.2-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-reactive-windows-forms2.2-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-reactive-debugger2.2-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-reactive-experimental2.2-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-reactive-runtime-remoting2.2-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-reactive-observable-aliases0.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-reactive-providers2.2-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-reactive-platformservices2.2-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-reactive-linq2.2-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-reflection-context4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-runtime-caching4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-runtime-durableinstancing4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-runtime4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-servicemodel-discovery4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-servicemodel-routing4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing mono-4.0-service (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-serviceprocess4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-threading-tasks-dataflow4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-web-abstractions4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-web-dynamicdata4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-web-extensions-design4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-web-mobile4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-web-razor2.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-web-regularexpressions4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-web-routing4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-windows-forms-datavisualization4.0a-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-windows4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-workflow-activities4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-workflow-componentmodel4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-workflow-runtime4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-xml-serialization4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-tasklets4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-webmatrix-data4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-xbuild-tasks4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing mono-utils (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmonoboehm-2.0-1 (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmonosgen-2.0-dev (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmonosgen-2.0-1 (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libzmq5:amd64 (4.3.4-2) ...
Removing libnorm1:amd64 (1.5.9+dfsg-2) ...
Removing libvorbisfile3:amd64 (1.3.7-1build2) ...
Removing libvorbis0a:amd64 (1.3.7-1build2) ...
Removing libogg0:amd64 (1.3.5-0ubuntu3) ...
Removing libpfm4:amd64 (4.11.1+git32-gd0b85fb-1ubuntu0.1) ...
Removing libpgm-5.3-0:amd64 (5.3.128~dfsg-2) ...
Removing libprotobuf-lite23:amd64 (3.12.4-1ubuntu7.22.04.1) ...
Removing libqdbm14 (1.8.78-11build3) ...
Removing librabbitmq4:amd64 (0.10.0-1ubuntu2) ...
Removing libre2-9:amd64 (20220201+dfsg-1) ...
Removing libsnappy1v5:amd64 (1.1.8-1build3) ...
Removing libtdb1:amd64 (1.4.5-2build1) ...
Removing libtidy5deb1:amd64 (2:5.6.0-11build2) ...
Removing libtimedate-perl (2.3300-2) ...
Removing libtinfo-dev:amd64 (6.3-2ubuntu0.1) ...
Removing libtommath1:amd64 (1.2.0-6build3) ...
Removing libu2f-udev (1.1.10-3build2) ...
Removing liburi-perl (5.10-1) ...
Removing libvulkan1:amd64 (1.3.204.1-2) ...
Removing libx11-xcb1:amd64 (2:1.7.5-1) ...
Removing xserver-common (2:21.1.4-2ubuntu1.7~22.04.1) ...
Removing x11-xkb-utils (7.7+5build4) ...
Removing libxaw7:amd64 (2:1.0.14-1) ...
Removing libxcb-dri2-0:amd64 (1.14-3ubuntu3) ...
Removing libxcb-dri3-0:amd64 (1.14-3ubuntu3) ...
Removing libxcb-glx0:amd64 (1.14-3ubuntu3) ...
Removing libxcb-present0:amd64 (1.14-3ubuntu3) ...
Removing libxcb-sync1:amd64 (1.14-3ubuntu3) ...
Removing libxcb-xfixes0:amd64 (1.14-3ubuntu3) ...
Removing libxmu6:amd64 (2:1.1.3-3) ...
Removing libxshmfence1:amd64 (1.3-1build4) ...
Removing libxxf86vm1:amd64 (1:1.1.4-1build3) ...
Removing libz3-dev:amd64 (4.8.12-1) ...
Removing libz3-4:amd64 (4.8.12-1) ...
Removing libzip4:amd64 (1.7.3-1ubuntu2) ...
Removing libzstd-dev:amd64 (1.4.8+dfsg-3build1) ...
Removing mlock (8:2007f~dfsg-7build1) ...
Removing mono-mcs (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing netstandard-targeting-pack-2.1 (2.1.0-1) ...
Removing shtool (2.0.8-10) ...
Removing sound-theme-freedesktop (0.8-2ubuntu1) ...
Removing xul-ext-ubufox (3.4-0ubuntu1.17.10.1) ...
Removing lib32stdc++6 (12.1.0-2ubuntu1~22.04) ...
Removing lib32gcc-s1 (12.1.0-2ubuntu1~22.04) ...
Removing libmono-cecil-private-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-microsoft-web-infrastructure1.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-web-extensions4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-data-linq4.0-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-reactive-core2.2-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing libmono-system-reactive-interfaces2.2-cil (6.12.0.182-0xamarin1+ubuntu2004b1) ...
Removing msbuild-libhostfxr (3.0.0.2019.04.16.02.13-0xamarin4+ubuntu2004b1) ...
Removing msbuild-sdkresolver (1:16.10.1+xamarinxplat.2021.05.26.14.00-0xamarin2+ubuntu2004b1) ...
Processing triggers for man-db (2.10.2-1) ...
Processing triggers for postgresql-common (250.pgdg22.04+1) ...
Building PostgreSQL dictionaries from installed myspell/hunspell packages...
Removing obsolete dictionary files:
  /var/cache/postgresql/dicts/en_us.affix
  /var/cache/postgresql/dicts/en_us.dict
  /usr/share/postgresql//14/tsearch_data/en_us.affix
  /usr/share/postgresql//14/tsearch_data/en_us.dict
Processing triggers for libc-bin (2.35-0ubuntu3.1) ...

********************************************************************************
=> Large misc. packages: Saved 11GiB
********************************************************************************


********************************************************************************
=> Tool cache: Saved 3.2GiB
********************************************************************************

               total        used        free      shared  buff/cache   available
Mem:           6.8Gi       623Mi       4.5Gi        20Mi       1.7Gi       5.9Gi
Swap:             0B          0B          0B

********************************************************************************
=> Swap storage: Saved 4.0GiB
********************************************************************************


================================================================================
AFTER CLEAN-UP:

$ df -h /

Filesystem      Size  Used Avail Use% Mounted on
/dev/root        84G   17G   68G  20% /
$ df -a /

Filesystem     1K-blocks     Used Available Use% Mounted on
/dev/root       87204404 16863852  70324168  20% /
$ df -a

Filesystem     1K-blocks     Used Available Use% Mounted on
/dev/root       87204404 16863852  70324168  20% /
devtmpfs         3550836        0   3550836   0% /dev
proc                   0        0         0    - /proc
sysfs                  0        0         0    - /sys
securityfs             0        0         0    - /sys/kernel/security
tmpfs            3555304      172   3555132   1% /dev/shm
devpts                 0        0         0    - /dev/pts
tmpfs            1422124     1096   1421028   1% /run
tmpfs               5120        0      5120   0% /run/lock
cgroup2                0        0         0    - /sys/fs/cgroup
pstore                 0        0         0    - /sys/fs/pstore
bpf                    0        0         0    - /sys/fs/bpf
systemd-1              -        -         -    - /proc/sys/fs/binfmt_misc
hugetlbfs              0        0         0    - /dev/hugepages
mqueue                 0        0         0    - /dev/mqueue
debugfs                0        0         0    - /sys/kernel/debug
tracefs                0        0         0    - /sys/kernel/tracing
fusectl                0        0         0    - /sys/fs/fuse/connections
configfs               0        0         0    - /sys/kernel/config
none                   0        0         0    - /run/credentials/systemd-sysusers.service
/dev/loop0         65024    65024         0 100% /snap/core20/1891
/dev/loop1        114688   114688         0 100% /snap/lxd/24322
/dev/loop2         54656    54656         0 100% /snap/snapd/19361
/dev/sda15        106858     6182    100677   6% /boot/efi
binfmt_misc            0        0         0    - /proc/sys/fs/binfmt_misc
/dev/sdb1       14341128       28  13590816   1% /mnt
tmpfs             711060       12    711048   1% /run/user/1001
tmpfs            1422124     1096   1421028   1% /run/snapd/ns
nsfs                   0        0         0    - /run/snapd/ns/lxd.mnt
================================================================================


/dev/root:

********************************************************************************
=> Saved 41GiB
********************************************************************************

overall:

********************************************************************************
=> Saved 45GiB
********************************************************************************
```
