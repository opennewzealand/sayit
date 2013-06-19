---
layout: default
title: Using SayIt
---

Using SayIt
===========

Some brief things to note on using SayIt in its current form.

Section display
---------------

Speeches within a section are displayed in timestamp order, if timestamps are
provided, or in insertion order otherwise.

A section page displays a hierarchical tree of its descendant sections, and
then any speeches linked to that section. This means speeches should only be
associated with a non-bottom level Section if they are a "post-amble", as it
were. This could be swapped, so that preamble speeches could be easily
included; this seems like it would be more common.

Alternatively, the code could try and use timestamps to intersperse subsections
and speeches, but this seems potentially complex.

