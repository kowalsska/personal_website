---
title: "Portuguese Lessons"
date: 2025-05-01T00:00:00+00:00
draft: false
---

<img src="../../img/portuguese_lessons.png">

<a href="https://magdawg.github.io/portuguese-lessons/" target="_blank">🌐 magdawg.github.io/portuguese-lessons</a>

I've been learning European Portuguese with my teacher Mariana. Each week we have a class: grammar, vocabulary, a dialogue, exercises. I take notes during the session, but between classes the notes would just sit in a doc and I rarely went back to them. Passive notes are easy to create and easy to ignore.

I wanted something more useful: an interactive study page for each lesson, with exercises I could actually do in the browser, scored in real time, with the grammar and vocabulary laid out clearly. And I wanted it to happen automatically, not as a manual project I'd maintain for two lessons and then abandon.

## How it works

Mariana's class gets transcribed by [Granola](https://www.granola.ai/), which records and transcribes meetings. After each class, I run a Claude Code skill (`/portuguese-lesson`) pointed at the meeting's transcript and notes. Claude reads what was covered (which grammar constructions, which vocabulary, what the dialogue was about) and writes a complete study page from it. One command, one new lesson page.

Each page has:
- **Grammar section**: the rule explained in English with examples from the actual class
- **Vocabulary**: words and phrases from that session, with translations
- **Reconstructed dialogue**: a clean version of the dialogue we practiced, with a vocabulary callout
- **Exercises**: multiple choice, fill-in-the-blank, conjugation drills, all self-scoring in the browser with no server needed

Homework (Mariana's scanned worksheets and listening tracks) gets added with a second skill. I drop the raw files into a folder, run `/portuguese-homework <date>`, and Claude reads each page, optimises the images, adds audio players, and attaches a "Trabalho de casa" section to that lesson. The originals get replaced with the optimised versions.

The home page lists all lessons as cards, and prev/next navigation is automatic. No other files need editing when a new lesson is added.

## Technical choices

The site is built with [Eleventy](https://www.11ty.dev/), a simple static site generator that stays out of the way. Lessons are HTML files in `src/aulas/`, sharing a base Nunjucks layout that adds the header, navigation, lightbox for worksheet images, and the scoring engine. Everything runs in the browser, there's no backend.

The scoring engine is vanilla JavaScript. It reads the exercise markup, checks answers on submit, and shows per-exercise feedback with a total score. Simple enough to reason about and modify without a framework dependency.

GitHub Actions builds the site on every push to `main` and deploys to GitHub Pages. The whole pipeline from class to published study page takes about five minutes.

The most interesting part of this project is the skill that generates lessons. Writing it required thinking carefully about what makes a good study page versus a good class transcript. A transcript captures what happened; a study page needs to be usable standalone by someone who wasn't in the room. Claude does the synthesis: pulling the teachable grammar points out of the dialogue, writing clear rule explanations, inventing exercises that test the right things. The skill just provides the structure and constraints.

**Stack:** Eleventy (11ty) · Nunjucks · JavaScript · GitHub Actions · GitHub Pages · Claude Code (MCP + skills)
