---
title: "Nix for JavaScript"
date: 2021-07-15T18:11:27-00:00
draft: true
---

Continuing along with my [previous post]({{< relref "nix-is-worth-the-complexity.md" >}}), I wondered what it would look like to use project-specific Nix code in a real workspace.

# The problem

Several developers are working on a project. Each have their own thoughts and opinions on how their workspace should be setup and, especially so, what level of tolerance they have of project dependencies polluting the workstation's system-wide setup. I, for one, will go to great lengths to make sure everything is sandboxed. I'm not about to sacrifice the integrity of other development environments for any one environment.

# The solution spec

- Something cross-platform to \*nix systems
- I need consistency with deterministic builds of all dependencies
- I'll allow this thing to take a performance hit on first invocation, but it needs to be fast when resuming a project
- Rollback to previous, known-working software should be 100% supported
- Mapping exactly what I need for a project and none of what I don't
- One project's dependencies don't interfere with a different project (e.g., making dependencies globally available by default)
- Easy to use
- The easiest path for use is the ideal, best-practice path

# The solution so far

Using [`node2nix`][github-node2nix], I've been able to convert a project's `package.json` and constraints in `package-lock.json` into nix expressions. Something to note here is that `npm` already supports non-global sandboxing of its JavaScript dependencies. Why involve Nix?

Where `npm` falls short is knowing when/how to install native dependencies. Consider ImageMagick support. There is an `npm` package, [`gm`][npm-gm], that has [GraphicsMagick][homepage-graphicsmagick] and [ImageMagick][homepage-imagemagick] support. GraphicsMagick has drop-in replacement of headers provided by ImageMagick. Do you install that instead? How do you install it on MacOS? Ubuntu? Arch? Debian? CentOS? RHEL? Any of the other OS's out there?

With Nix you can override the generated expression and say, "When building `gm`, use the GraphicsMagick package with the dev headers as a build dependency. But I don't need anything else related to GraphicsMagick at runtime."

How `node2nix` operates is it generates everything you need to build a Node.js project (which uses `npm`) in <TODO />.

Given a workspace with `node2nix` generating the package definition in the root of the repo, you can write `shell.nix` (how to make the project available in your shell session, used with `direnv`'s `use nix`), you can call it out like so:

```nix
# File: shell.nix
{ pkgs ? import <nixpkgs> {} }:
let
  nodePackages = import ./default.nix {};
in
stdenv.mkShell {
  buildInputs = [
    nodePackages.package
    pkgs.ffmpeg
  ];
}
```

[github-node2nix]: https://github.com/svanderburg/node2nix
[npm-gm]: https://www.npmjs.com/package/gm
[homepage-graphicsmagick]: http://www.graphicsmagick.org
[homepage-imagemagick]: https://imagemagick.org
