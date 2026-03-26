+++
title = "Chezmoi ou cheztoi?"
description = "Dotfiles in the year 2026."
date = 2026-03-25
authors = ["Aksum"]

[taxonomies]
tags = ["dotfiles", "chezmoi", "mise", "cli-tools", "rust", "devops"]
+++

There is a specific kind of dread that hits when you sit down with a fresh machine. Not the existential kind. The practical kind. The kind where your fingers type `ll` and the terminal stares back at you with the dead-eyed look of a stock `ls`, no colors, no icons, no nothing; just a flat list of filenames rendered in the default font of a man who has given up. Your aliases are gone. Your prompt looks like it belongs in a sysadmin tutorial from 1999. You are, briefly, nobody.

I have been in this situation enough times to have developed opinions about it.

## The old regime

For years, the answer was a combination of bash scripts and Ansible. And look, it worked. Mostly. The bash scripts handled the quick installs, the symlinks, the "just get me to a usable state" layer. Ansible covered the heavier stuff like packages, configs, the occasional cursed `lineinfile` task that you write once and never look at again because you know it works and you're afraid of what you'll find if you look too closely.

The problem wasn't that it didn't work. The problem was the ceremony. A new machine meant running the bootstrap script, watching it fail at step 3 because something changed upstream, manually patching whatever broke, re-running, waiting for Ansible to churn through twenty tasks to confirm that nineteen of them were already fine. `CHANGED` where it shouldn't be. `OK` where you weren't sure why. The idempotency was theoretical.

It was infrastructure-as-code for infrastructure that was technically just your `~/.bashrc`.

## The upgrade campaign

At some point I made a quiet decision to systematically replace every aging Unix utility I use daily with something newer. The criteria were loose; faster, friendlier output, and if it happens to be written in Rust, so much the better. Not out of ideology. Just because the Rust ecosystem has been unusually good at producing tools that are both fast and thoughtfully designed.

Some hits so far:

- `ls` → [`eza`](https://eza.rocks/) — git status in your file listing, icons, tree view, colors that don't look like they were chosen in 1984
- `cd` → [`zoxide`](https://github.com/ajeetdsouza/zoxide) — a smarter cd command, which remembers your most frequently used directories
- `cat` → [`bat`](https://github.com/sharkdp/bat) — syntax highlighting, line numbers, git diff markers, automatic paging
- `find` → [`fd`](https://github.com/sharkdp/fd) — sensible defaults, `.gitignore` awareness, actually memorable syntax
- `grep` → [`ripgrep`](https://github.com/BurntSushi/ripgrep) — you know why
- `du` → [`dust`](https://github.com/bootandy/dust) — disk usage that shows you what you need to see instead of a wall of numbers
- `ps` → [`procs`](https://github.com/dalance/procs) — color, search, tree view, doesn't make you feel like you're reading a kernel log
- `tmux` → [`zellij`](https://github.com/zellij-org/zellij) — a workspace aimed at developers, ops-oriented people, and anyone who loves the terminal

The old tools did the job. The new ones do the job and remind you that software is allowed to be fast and pleasant. It's a low bar. They clear it.

Dotfiles needed the same treatment.

## Enter chezmoi

[chezmoi](https://www.chezmoi.io/) is a dotfile manager written in Go. Not Rust. I know. I was briefly disappointed. I got over it.

The core idea is elegant: your dotfiles are managed as *source state*, not as symlinks or raw copies strewn across a git repo. chezmoi keeps a source directory, by default `~/.local/share/chezmoi`, and applies it to your home directory. You edit files in the source directory. You apply. The actual dotfiles in `$HOME` are the output.

This sounds like a minor distinction until you realize what it enables:

**Templates.** Your `.gitconfig` can have your work email on the work machine and your personal email everywhere else, with zero manual intervention. One source, multiple outputs. Jinja-style templating with actual data from the environment.

**Encrypted secrets.** Need your API keys or SSH config in your dotfiles? chezmoi integrates with `age`, `gpg`, and password managers like 1Password and Bitwarden. The encrypted version lives in the repo. The decrypted version lands on your machine when you apply. Nothing sensitive ever touches the git history.

**Cross-machine divergence, by design.** Machines are different. chezmoi doesn't pretend otherwise.

The migration itself was almost insultingly easy. `chezmoi init`, `chezmoi add ~/.bashrc`, repeat for the files that mattered. `chezmoi cd` to inspect the source directory. `chezmoi apply` to push changes out. No roles. No `vars/`. No playbook that starts with `- hosts: localhost` and somehow takes four minutes to run.

The Ansible playbook that did roughly the same job was 420 lines. The chezmoi equivalent is my actual dotfiles, tracked sensibly, with a 23-line `chezmoi.toml` for machine-specific variables. That's it. That's the whole thing.

## chezmoi + mise = devops heaven

Here is where it gets good.

[mise](https://mise.jdx.dev/) is a runtime version manager for Rust, Node, Python, Ruby, Go, Java, whatever you're running. It works per-project via a `mise.toml` in the project root, or globally via `~/.config/mise/config.toml`. It replaced `nvm`, `pyenv`, `rbenv`, and three other things I had running simultaneously and occasionally conflicting with each other. One tool. One source. Done.

The combination works like this: chezmoi manages your environment, your shell config, your tool config, including `~/.config/mise/config.toml`. When you `chezmoi apply` on a fresh machine, mise's global config is already there. mise takes it from there, installing the right runtime versions and making the environment actually usable.

New machine. Run `chezmoi init --apply your/dotfiles-repo`. Everything lands in the right place. Shell config, editor config, git config, global mise setup. Then `mise install`. Done. The whole ceremony collapses to two commands.

It is the closest thing to teleportation that boring, non-magical software engineering has to offer. Your environment follows you. The machine is just hardware.

## The verdict

The bash scripts served their time. Ansible was genuinely the right tool for a while, back when the goal was managing more than one machine with more configuration complexity than I currently carry. But dotfiles are not infrastructure. They're closer to luggage. You want them packed well, versioned, and easy to unpack anywhere.

chezmoi packs them well.

Your dotfiles are no longer a liability, a tangled bootstrapping ritual that you half-remember and half-hope still works. They're a portable, versioned, templated representation of your environment, managed by a tool that has thought carefully about exactly this problem.

I'm still afraid of `rm -rf /`. But I'm less afraid of it than I used to be.
