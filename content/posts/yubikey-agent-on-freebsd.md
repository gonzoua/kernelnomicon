---
title: yubikey-agent on FreeBSD
date: '2020-06-04T00:03:01'
draft: false
author: Oleksandr Tymoshenko
tags:
- freebsd
categories:
- FreeBSD
---

Some time ago [Filippo Valsorda](https://filippo.io/) wrote [yubikey-agent](https://github.com/FiloSottile/yubikey-agent), seamless SSH agent for YubiKeys. I really like YubiKeys and worked on the FreeBSD support for U2F in Chromium and pyu2f, getting yubikey-agent ported looked like an interesting project. It took some hacking to make it work but overall it wasn't hard. Following is the roadmap on how to get it set up on FreeBSD. The actual details depend on your system (as you will see)

The first step is to set up middleware for accessing smart cards (YubiKey implements CCID smart-card protocol). The pcsc-lite package provides a daemon and a library for clients to communicate with the daemon. ccid is a plugin for pcsc-lite that implements the actual CCID protocol over USB. devd rules are to make the daemon re-scan USB devices on hotplug

```
sudo pkg install ccid pcsc-lite
sudo mkdir -p /usr/local/etc/devd
sudo tee /usr/local/etc/devd/pcscd.conf << __EOF__
attach 100 {
        device-name "ugen[0-9]+";
        action "/usr/local/sbin/pcscd -H";
};
detach 100 {
        device-name "ugen[0-9]+";
        action "/usr/local/sbin/pcscd -H";
};
__EOF__

sudo service devd restart
sudo sysrc pcscd_enable="YES
sudo service pcscd start
```

go and git are build requirements for the app. go get command is required because FreeBSD support  was only recently merged into piv-go and the latest release, referenced in go.mod, still does not have it. go install command installs the app in ~/go/bin/. When new version of piv-go is released and yubikey-agent switches to using it, all these commands can be replaced with single `go get github.com/FiloSottile/yubikey-agent` command.

```
sudo pkg install go git
git clone https://github.com/FiloSottile/yubikey-agent.git
cd yubikey-agent
go get github.com/go-piv/piv-go@a3e5767e
go build
go install
```

The binary is in ~/go/bin/ directory, you can add it to your $PATH to type less. 

The next step is setting up a Yubikey, it's well documented on the official site. One caveat though is if PIN length is less than 6 chars the setup fails with the somewhat confusing message: "‼️  The default PIN did not work."

The actual usage of yubikey-agent depends on your setup. First of all, yubikey-agent is a "eventually GUI" app. At some point, when entering PIN is required it starts pinentry command which task is to present user with a dialog entry, get PIN, and pass it to the app. There are multiple pinentry flavors, with different front-ends: TTY, Qt, GTK. gopass module used in yubikey-agent does not work with plain TTY backend, and requires pinentry to be a GUI app. On Debian/Ubuntu users can switch between flavors and make /usr/bin/pinentry point to either Qt5 or GTK version, but in FreeBSD /usr/local/bin/pinentry is always TTY one. I worked around it by installing pinentry-qt5 package and making symlink.

```
sudo pkg install pinentry-qt5
sudo ln -s /usr/local/bin/pinentry-qt5 /usr/local/bin/pinentry
```

If you need /usr/local/bin/pinentry to be TTY version for some other stuff, there may be a problem. How to work around this depends on your requirements. I don't have a ready recipe.

Because yubikey-agent is "eventually GUI" you can either start it in .xsession or .xinitrc files or start it some other way with DISPLAY env variable set. Other than that official documentation is a good source of information on how to use it.
