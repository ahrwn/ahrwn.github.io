---
title: Batch edit with ed
date: 2025-02-09
teaser: Explore the greatness of ed
slug: 2023-01-25-batch-edit-files-with-ed
---

The other day at work I needed to edit 200 files at once across dozens of repos. I wanted to do something pretty simple: basically, I had files that looked like this:

    foo:
      - bar
      - baz
      - bananas
    

and I wanted to insert an extra line after the `baz` line that said `elephant`

    foo:
      - bar
      - baz
      - elephant
      - bananas
    

I had one extra weird requirement which was that some of the lines were indented with 2 spaces, and some with 4 spaces. The `- elephant` line needed to have the same indentation as the previous line.

I didn’t feel like writing a program to do this (perl would be perfect, but I don’t really remember perl at all), so I wanted to use a command line tool! A vim macro could do it, but how do you save a vim macro to a file again? I forget! I couldn’t think of how to do it with sed at the time, though in retrospect you could do something like `s/(.+)- baz/\1- baz\n\1- elephant`.

In a surprising turn of events, I ended up using the `ed` editor to do this task, and it was really easy and simple to do! In this blog post I’ll make the case that if you have something you might normally accomplish with a Vim macro, you might conceivably want to use Ed for it!

### [what’s ’ed'?](#what-s-ed)

`ed` is this sort of terrifying text editor. A typical interaction with `ed` for me in the past has gone something like this:

    $ ed
    help
    ?
    h
    ?
    asdfasdfasdfsadf
    ?
    <close terminal in frustration>
    

Basically if you do something wrong, ed will just print out a single, unhelpful, `?`. So I’d basically dismissed `ed` as an old arcane Unix tool that had no practical use today.

`vi` is a successor to `ed`, except with a visual interface instead of this `?`

### [surprise: Ed is actually sort of cool and fun](#surprise-ed-is-actually-sort-of-cool-and-fun)

So if Ed is a terrifying thing that only prints `?` at you, why am I writing a blog post about it? WELL!!!!

On April 1 this year, Michael W Lucas published a new short book called [Ed Mastery](https://www.michaelwlucas.com/tools/ed). I like his writing, and even though it was sort of an april fool’s joke, it was ALSO a legitimate actual real book, and so I bought it and read it to see if his claims that Ed is actually interesting were true.

And it was so cool!!!! I found out:

*   how to get Ed to give you better error messages than just `?`
*   that the name of the `grep` command comes from ed syntax (`g/re/p`)
*   the basics of how to navigate and edit files using ed

All of that was a cool Unix history lesson, but did not make me want to actually use Ed in real life. But!!!

The other neat thing about Ed (that did make me want to use it!) is that any Ed session corresponds to a script that you can replay! So if I know Ed, then I can use Ed basically as a way to easily apply vim-macro-like programs to my files.

### [how I solved my problem with ed](#how-i-solved-my-problem-with-ed)

So! we have a file like this:

    foo:
      - bar
      - baz
      - bananas
    

and we want to add a line after `- baz` that says `- elephant`. Let’s do it!!

With Vim, I’d do it by:

1.  search for `baz`
2.  copy that line and paste it
3.  `s/baz/elephant`
4.  save & quit

We can translate that into an Ed script in a really pretty straightforward way!!

    /baz                 # search for `baz`
    .t.                  # copy that line and paste it on the next line
    s/baz/elephants      # on the second, pasted, line replace `baz` with `elephants`
    w                    # save
    q                    # quit
    

ed doesn’t actually have comments, so if you wanted to actually run this ed script you’ll have to remove the `#` things

Most of this is very similar to what you’d do in Vim – the `.t.` part of this is the most inscrutable bit, but I figured it out through some judicious use of Stack Overflow.

### [using the ed script](#using-the-ed-script)

Applying the ed script to the file I want to edit is easy! Here’s how;

    cat my-script.ed | ed file-to-edit.txt
    

(or you could write `ed file-to-edit.txt < my-script.ed`, but I always use cat and pipe in practice :) )

### [Ed is at least a little bit useful!!!!](#ed-is-at-least-a-little-bit-useful)

It was super surprising and delightful to me to find a practical use for Ed! To me the most compelling thing about Ed is that I use simple Vim macros a lot, and it’s a pretty direct way to translate a Vim macro into a way to batch edit a bunch of files.

I’m definitely not going to go telling everyone they should be using ed (it’s certainly not very user friendly!), but I think it’s neat. If you’re interested, I’d really recommend buying [Ed Mastery](https://www.michaelwlucas.com/tools/ed) – it’s quite short, I learned some neat Unix history from it, and now I have a new tool to use very occasionally!!
