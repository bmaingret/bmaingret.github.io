---
category: [system]
tags: [gpg, git, zsh, wsl]
title: "Using 1password, GPG and git for seamless commits signing"
---

Setting up gpg, git and 1password to have your git commits signed, whilestoring your GPG key passphrase into 1Password and unlocking it directly from your terminal.


<!--more-->

- [Requirements and setup](#requirements-and-setup)
- [Creating a GPG key](#creating-a-gpg-key)
- [Setting up git](#setting-up-git)
- [Setting up GitHub](#setting-up-github)
- [Setting up 1Password CLI](#setting-up-1password-cli)
- [Configuring gpg-agent](#configuring-gpg-agent)
- [Putting it all together](#putting-it-all-together)
  - [Finding your 1Password entry](#finding-your-1password-entry)
  - [Getting your GPG key grip](#getting-your-gpg-key-grip)
  - [Binding the two](#binding-the-two)
  - [Running at login](#running-at-login)
- [Testing it](#testing-it)
- [Sources](#sources)

## Requirements and setup

This was done on WSL2.

```console
❯ uname -a
Linux DESKTOP-AGPN69M 5.10.60.1-microsoft-standard-WSL2 #1 SMP Wed Aug 25 23:20:18 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux
❯ cat /etc/lsb-release
DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=20.04
DISTRIB_CODENAME=focal
DISTRIB_DESCRIPTION="Ubuntu 20.04.3 LTS"
```

Make sure you have git and gpg installed, and an active 1Password account.

You'll find written to use gpg2 when available on your system, but in my case both gpg and gpg2 were pointed to the same bin.

```console
❯ ls -l "$(which gpg)"
.rwxr-xr-x 1.1M root  6 Jan  2021 /usr/bin/gpg
❯ ls -l "$(which gpg2)"
lrwxrwxrwx 3 root  6 Jan  2021 /usr/bin/gpg2 -> gpg
❯ gpg --version
gpg (GnuPG) 2.2.19
```

I will use [jq](https://stedolan.github.io/jq/) but this is not essential.

```console
❯ jq --version
jq-1.6
```

## Creating a GPG key

This is a summary of [GitHub - Generating a new GPG key](https://docs.github.com/en/authentication/managing-commit-signature-verification/generating-a-new-gpg-key), that I followed.

Run the following command and follow instructions.

```console
❯ gpg --full-generate-key
```

Then list your fresh key.

```console
❯ gpg --list-secret-keys --keyid-format=long
/home/baptiste/.gnupg/pubring.kbx
---------------------------------
sec   rsa4096/0052A8D354A5C655 2022-02-09 [SC]
      9BA03414AB56590B6DB5369F0052A8D354A5C655
uid                 [ultimate] Baptiste Maingret (Home Desktop-WSL2) <baptiste.maingret@gmail.com>
ssb   rsa4096/A5B8C64E8929B475 2022-02-09 [E]
```
Look at the `sec` line and note the GPG key ID: `0052A8D354A5C655`.

Then we export the corresponding public key.

```console
❯ gpg --armor --export 0052A8D354A5C655
-----BEGIN PGP PUBLIC KEY BLOCK-----
# your public key
-----END PGP PUBLIC KEY BLOCK-----
```

Copy everything including the starting and ending blocks.

## Setting up git

First let's tell `git` which key to use. Using your GPG key ID, run:

```console
❯ git config --global user.signingkey 0052A8D354A5C655
```

**N.B** This will configure it globally, you may need to configure it per repository depending on your usage.

Then we will tell `git` to sign **every commit of every repository**. 

```console
❯ git config --global commit.gpgsign true
```

## Setting up GitHub

Instructions may change. Check online documentation [Adding a new GPG key to your GitHub account](https://docs.github.com/en/authentication/managing-commit-signature-verification/adding-a-new-gpg-key-to-your-github-account).

TL;DR. `Settings > Access > New GPG key`


## Setting up 1Password CLI 

Instructions may change. Check online documentation. [1Password CLI: Getting started](https://support.1password.com/command-line-getting-started/)

**N.B.** Version is hardcoded in URL, so make sure to check the official website to get latest URL.

```console
❯ curl -S https://cache.agilebits.com/dist/1P/op/pkg/v1.12.4/op_linux_amd64_v1.12.4.zip > op.zip
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 3810k  100 3810k    0     0  4897k      0 --:--:-- --:--:-- --:--:-- 4891k

❯ unzip op.zip -d op
Archive:  op.zip
 extracting: op/op.sig
  inflating: op/op

❯ gpg --keyserver hkps://keyserver.ubuntu.com --receive-keys 3FEF9748469ADBE15DA7CA80AC2D62742012EA22
gpg: key AC2D62742012EA22: public key "Code signing for 1Password <codesign@1password.com>" imported
gpg: Total number processed: 1
gpg:               imported: 1  

❯ gpg --verify op/op.sig op/op
gpg: Signature made Fri Jan 14 22:38:08 2022 CET
gpg:                using RSA key 3FEF9748469ADBE15DA7CA80AC2D62742012EA22
gpg: Good signature from "Code signing for 1Password <codesign@1password.com>" [unknown]
gpg: WARNING: This key is not certified with a trusted signature!
gpg:          There is no indication that the signature belongs to the owner.
Primary key fingerprint: 3FEF 9748 469A DBE1 5DA7  CA80 AC2D 6274 2012 EA22

❯ sudo mv op /usr/bin

❯ op --version
1.12.4
```

Try to sign in. 
```console
op signin my.1password.com your.email@example.com
```

## Configuring gpg-agent

We will make use of `gpg-preset-passphrase` to cache our passphrase for our key. For that we need to make sure `gpg-agent` allows it.

```console
❯ echo "allow-preset-passphrase" >> ~/.gnupg/gpg-agent.conf
```

## Putting it all together

### Finding your 1Password entry

I will assume you have a `1Password` entry storing your GPG key passphrase, with the name `GPG passphrase`.

```console
❯ op get item "GPG passphrase" | jq ".uuid"
"vmgevmdnbbuui3evhksdftjhju"
```

### Getting your GPG key grip

We list our keys and their key grips.

```console
❯ gpg --list-secret-keys --with-keygrip
/home/baptiste/.gnupg/pubring.kbx
---------------------------------
sec   rsa4096 2022-02-09 [SC]
      9BA03414AB56590B6DB5369F0052A8D354A5C655
      Keygrip = 80160C5055DA07978E939C0575A4E8DA0B1ECF27
uid           [ultimate] Baptiste Maingret (Home Desktop-WSL2) <baptiste.maingret@gmail.com>
ssb   rsa4096 2022-02-09 [E]
      Keygrip = C04ACB8C33AAA68943194D7D1A56954BF76B5C2C
```

Look at the `sec` block and at the `Keygrip` entry: `80160C5055DA07978E939C0575A4E8DA0B1ECF27`.

### Binding the two

We will ask the 1Password to retrieve the password and pass it directly to `gpg-preset-passphrase` specifying our key grip. Note that `gpg-preset-passphrase` will read `stdin` by default.

```console
op get item vmgevmdnbbuui3evhksdftjhju --fields password | /usr/lib/gnupg2/gpg-preset-passphrase --preset 80160C5055DA07978E939C0575A4E8DA0B1ECF27
```

If you weren't logged in 1Password, you will be asked to input your password. 

### Running at login

I am using `zsh` as a shell, so I will add the following to my `~/.zshrc` but should be able to do the same with `~/.bashrc` for instance. Note that if you are using [powerlevel10k](https://github.com/romkatv/powerlevel10k), you will need to put the following before the `instant-prompt` configuration.

```shell
function gpg_cache () {
        gpg-connect-agent /bye &> /dev/null # 1
        eval $(op signin my) # 2
        op get item vmgevmdnbbuui3evhksdftjhju --fields password | /usr/lib/gnupg2/gpg-preset-passphrase --preset 80160C5055DA07978E939C0575A4E8DA0B1ECF27 # 3
}
gpg_cache # 4
```

1. `gpg-agent` is automatically started when required, however since `gpg` is not used in this but we still require `gpg-agent` we need to make sure it is started. This is the best way I found to achieve it.
2. Login in `1Password`.
3. Using our one-liner to retrieve the passphrase and cache it.
4. Calling our beautiful function.

**N.B.** This will require you to log in each time you start a session. You could also simply remove the call to `gpg_cache`, and call it from your terminal.


## Testing it

Go in one of your git repository, and let's create a branch and try this out.

```console
❯ git checkout -b signing
Switched to a new branch 'signing'

❯ touch dirty

❯ git add dirty

❯ git commit -m "Trying signing"
[signing 1426360] Trying signing
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 chapter-2/dirty

❯ git log --show-signature -1
commit 1426360d301b88036feef02e00044e6ca62a9fd3 (HEAD -> signing)
gpg: Signature made Tue Feb 15 21:46:51 2022 CET
gpg:                using RSA key 9BA03414AB56590B6DB5369F0052A8D354A5C655
gpg: Good signature from "Baptiste Maingret (Home Desktop-WSL2) <baptiste.maingret@gmail.com>" [ultimate]
Author: Baptiste Maingret <baptiste.maingret@gmail.com>
Date:   Tue Feb 15 21:46:51 2022 +0100

    Trying signing
```

Remove our dirty work.

```console    
❯ git reset HEAD^

❯ rm dirty

❯ git checkout main
Switched to branch 'main'

❯ git branch -D signing
```

## Sources

* [1Password CLI - Getting Started](https://support.1password.com/command-line-getting-started/)
* [GitHub Docs - Commit signature](https://docs.github.com/en/authentication/managing-commit-signature-verification)
* [Stackoverflow - Prevent git from asking for the GnuPG password during signing a commit](https://stackoverflow.com/questions/38384957/prevent-git-from-asking-for-the-gnupg-password-during-signing-a-commit)
