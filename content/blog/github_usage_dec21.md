---
title: "Github keyboard shortcuts to move around faster and productively"
date: 2021-12-07T22:18:34+01:00
draft: true
---

If you browse Github repositories on your browser often, chances are you find yourself moving between files to review code, jumping between lines, reviewing *diffs* and other common source code related tasks.

Here are the keyboard shortcuts that I find myself using more often to navigate and interact with Github repositories:

- <kbd>Ctrl</kbd><kbd>k</kbd>: Open **command palette** with fuzzy search
  + From here you make a wide range of actions by just writting: where you want to, or what you want to do
  + Search is fuzzy, so you don't even need to type the complete command, or even correctly
  + Navigate between tabs, just type: issues, PRs, Actions, Settings
  + Navigate between repositories: delete the repository name, and start writing a different one which belogs to the user or organization
  + Run commands by typing <kbd>></kbd>. From here one can run things like: creating an issue, close a pull request or updating the current branch
- <kbd>t</kbd>: File finder
  + When you know the name of the file you are looking for, or part of it, just start typing it to filter the possible results
  + Easy to filter by extension or some other pattern in the file name: type part of the name, then `.` followed by the extension to filter further
- <kbd>l</kbd>: Go to line
  + With a file opened, jump directly to some line by typing the number
- <kbd>w</kbd>: Switch to a branch or tag
  + To quickly change to some branch or tag if you know the name or part of the name of it
- <kbd>e</kbd>: Edit file
  + I don't find myself using this one much, but some quick or small changes it may be useful when you are not working with your local editor
- <kbd>.</kbd>: Open in web editor
  + If not working in your own machine, only have access to a browser, or don't have access to your everyday source code editor or IDE, this option gives you an online IDE. It is an online instance of [VSCode](https://code.visualstudio.com/), with all of its features, one of the outcomes of Microsoft buying Github.

Full reference of the keyboard shortctus and command palette options:
> [Github command palette](https://docs.github.com/en/get-started/using-github/github-command-palette)
> [Full list of Github keyboard shortcuts](https://docs.github.com/en/get-started/using-github/keyboard-shortcuts)