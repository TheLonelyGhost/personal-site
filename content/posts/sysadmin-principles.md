---
draft: true
created: 2021-04-12T11:35:00-05:00
title: 10 principles of a good sysadmin
modified: 2021-04-12T11:35:00-05:00
---

This seems to be a common topic of conversation, so I figure I should put it on paper (so to speak) what I value as a systems administrator, or "sysadmin."

First, some common terminology I tend to use:

<dl>
<dt><a name="def-compile-time">Compile time</a></dt>
<dd>For sysadmins, this means scripts that install and configure the given application. This is generally able to be sandboxed and tested more easily than <a href="#def-runtime">runtime</a>.</dd>
<dt><a name="def-runtime">Runtime</a></dt>
<dd>For sysadmins, this means operational stuff after a service and operating system have been configured. This is generally used in contrast with <a href="#def-compile-time">compile time</a>.</dd>
<dt><a name="def-vcs">Version control system (VCS)</a></dt>
<dd>A system typically built to track changes to plain text files such as source code. Examples of this are git, subversion, mercurial, and bazaar.</dd>
<dt><a name="def-sdlc">Software development lifecycle (SDLC)</a></dt>
<dd>The workflow that takes a bit of software from a half-baked idea in a persons brain all the way to full implementation and running in production. This involves the process of coding, testing, and deploying.</dd>
</dl>

[VCS]: #def-vcs
[runtime]: #def-runtime
[compile time]: #def-compile-time

## Principle #1: Keep it simple

Simply put, fewer moving parts means fewer spots to check at runtime when something inevitably breaks. Similarly, less complexity means it is easier to keep the entire system in your head while troubleshooting.

## Principle #2: Ensure it can be reproduced

This means a few things:

- Write a script for everything you do. Even the one-off tasks.
- Test things in a sandbox whenever possible. Reset that sandbox to default state to ensure it works in a "clean room" setting before considering it finished.

### Script it out

{{% tip title="Tips:" %}}

- Keep scripts in some central location to share with your team of sysadmins
- [Version control systems][VCS] are best for iterating on these scripts
- Make the [VCS] repo private, lest credentials are mistakenly hardcoded

{{% /tip %}}

Following this approach allows your teammates to benefit from your expertise, even if you're not present.

{{% example %}}

**Example:** Updating DNS in a manner consistent with how it was done before. Do I need to understand the fundamentals of DNS and networking? No, I just need to understand how the script you wrote changes for my purposes so I can blindly execute it with the relevant changes.

{{% /example %}}

Another benefit is it allows you to walk away mid-thought, then come back later without having to rely on your memory to find where you left off.

## Principle #3: Keep it close to stock

By keeping the configuration close to the out-of-box configuration this maximizes the likelihood that a Stack Overflow answer or blog post or some other search result will apply to your current situation.

If things need to deviate from stock settings, keep notes indicating the intent. Maybe these are comments inline. Maybe they're semantic commit messages in your [VCS]. Maybe it's some long-form document that is linked from one of those places. No matter the case, keep the reference or the content of the explained intent close to the source code for posterity.

## Principle #4: Magic is bad

If you don't understand how a thing works, fix that. Learn about it. Pull back that abstraction layer and look under the hood.

Why is magic bad? When it comes to troubleshooting, how can you be certain it isn't a bug in the magical tool's implementation? How can it logically be ruled-in/-out?

This principle does not preclude you from using the magical tool. It does mandate that you dispel the magical attribute of it and understand how it is implemented.

## Principle #5: No development tools on the server

Development should never happen on a production-level system. If it does, your SDLC
