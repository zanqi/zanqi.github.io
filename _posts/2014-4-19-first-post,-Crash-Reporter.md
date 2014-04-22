---
layout: post
category: lessons
tagline:
tags: [ELF, project]
---
{% include JB/setup %}

Alright! Blog is barely setup. Let me publish my first post. Boom! I do have something to share though. Here it is.

### Crash Reporter

I recently worked on a course project: crash reporter. It can be linked with ELF executable. When it crashes, the reporter will display the back trace to give us more information about the crash. Here's the [project repo](https://github.com/zanqi/CrashReporter).

If you want to build your own, this [instruction from the course](https://courseware.stanford.edu/pg/assignments/view/371797/assignment-6-crash-reporter) provides excellent guides and hints. To get the starter code, checkout my repo and revision:

    $ git clone https://github.com/zanqi/CrashReporter.git
    $ git checkout ee221c65588ad643114fdbf74d41cb2f6950aeba

### Hints

One behavior of the kernel can caused one time to wrap his head around:

>The top of the stack is a little wonky after a signal because the innermost stack frame is forced off and replaced by the kernel's signal-dispatching function.

Therefore, to compose the full picture of the stack trace, you must piece the information from two sources together:

1. from the signal handler caller chain
2. from the %eip stored in the signal context. This is to recover the frame that is forced off by kernel.

Have fun!