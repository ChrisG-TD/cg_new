---
layout: 
title: "Better Late than Never"
date: 2025-10-08
author: 
pin: false
categories: [Learning & Development, Developer Tools]
description: A veteran developer's late discovery of Jupyter notebooks and how they transformed learning new languages, from React to Rust, while navigating the crucial balance between exploration and production-ready code.
tags: [jupyter, learning_journey, development_workflow, career_development, python, rust, react, technical_writing, developer_experience, devlog]
---





I’ll admit, I felt a bit foolish the first time I launched a Jupyter notebook. Here I was, a veteran who’d spent years crafting Python-based production pipelines, debugging tools that touched everything from assets to rendering (and everything in between), and diving deep into procedural rigging logic, vector spaces, quaternions, and transformation matrices, only to discover a tool that seemingly every data scientist and their dog had been using forever.

My tech journey wasn’t conventional. With an undergrad in information science (peppered with formal coding classes), I sidestepped the typical CS path. Instead, I thrived as a bridge between developers and creative teams, then later as a TD at major studios, solving quirky problems at the crossroads of art and code.
Well, I did spend a few years working as a software developer during grad school, and even founded and ran a software and IT company, but that’s a story for another time.

And somehow, through all that, I completely missed the Jupyter notebook train.

## What Took Me So Long?

In hindsight, I viewed notebooks as “beginner tools” or strictly for data science. I was cozy in my text editors and IDEs. Spinning up Python scripts, writing shaders, and debugging production code? No problem.

But recently, as I’ve expanded into React, Deeper C++, and Rust, I’ve run into a new kind of friction, one that’s less about syntax and more about mindset. I wanted to test ideas quickly, see instant output, document my thoughts alongside code, and iterate fast. My usual workflow felt clunky, like suiting up in scuba gear to splash in the shallow end. It reminded me of a chat from a couple years back with an instructor wrestling through basic scripting lessons the old-fashioned way, if only notebooks had crossed my radar sooner, might’ve smoothed things out a bit.

Then, a few years ago, the relentless buzz on Twitter (now X) about notebooks finally got to me. I caved and tried one.

Oh.

*Oh.*

## The Power I Was Missing

For anyone in a similar boat, here’s why Jupyter notebooks are a game-changer:

**Live feedback loops.** Write a code cell, run it, and see results right below. No compilation wait, no editor-terminal juggling. Code and output coexist in one fluid document. When learning React hooks, I tested each pattern in isolation, inspected state at every step, and built complexity gradually.

**Built-in documentation.** Mix Markdown, code, and visualizations seamlessly. This was a revelation for Rust’s ownership model: I’d write explanatory text, add code showing borrowing rules, see the compiler’s complaints, then annotate why it failed. My notebooks became living learning journals. For example, one cell clarified a borrow checker error by showing how a mutable reference clashed with an immutable one, saving hours of head-scratching.

**Consequence-free playgrounds.** Curious if C++ move semantics behave as expected? Spin up a cell, write a simple class with print statements, move objects, and check the outcome. No need to scaffold an entire project to test a hunch.

Recently, I prototyped a data transformation pipeline in a notebook. Inline plots visualized each step, exposed where things broke, and let me tweak logic on the fly. What could’ve been a half-day of print-debugging took just an hour.

I’ve gotta say, I love the learning. If you’re diving into development or aiming to become a TD, start here. Use notebooks, stay curious, and steer clear of the “vibe coding” trend until you can solve problems without AI doing the heavy lifting.

## How Notebooks Changed My Learning Game

Here’s the real magic: **notebooks slashed the effort needed to experiment.**

Learning React, I wasn’t building apps right away. I was puzzling over questions like, “How does useEffect’s cleanup function *really* work?” or “What’s the difference between these state update patterns?” In a notebook, I answered those in minutes, not hours.

For C++, I created a “confusion notebook.” Every time something baffled me, I’d add a minimal example. Template metaprogramming woes? Start with the simplest template, inspect its output, then add complexity step-by-step. It’s like pair-programming with the compiler.

Rust was especially fun. I’d write code I *thought* would work, get scolded by the borrow checker, then use Markdown to explain why it was right. One notebook captured a eureka moment when I finally grasped why a lifetime annotation fixed a dangling reference. Those notebooks are now my most treasured learning artifacts.

## The Big BUT (And It’s Important)

A word of caution, especially for new coders: Jupyter notebooks are *fantastic*, but they can become a cozy trap. I’ve seen beginners get so comfortable in notebooks that moving to an IDE feels daunting. They miss out on version control, project structure, or reproducible workflows, leaving code stranded in isolated islands.

**Notebooks are for exploration and learning, not production.**

Signs it’s time to switch to an IDE:
- You’re copying code between notebooks to reuse functions.
- Your notebook exceeds a few hundred lines.
- You’re thinking, “This could be a real project.”
- You need collaboration beyond sharing static notebooks.
- You want robust debugging, testing, or deployment.

Modern IDEs like VS Code, PyCharm, or CLion borrow notebook-like features, inline visuals, interactive debugging, integrated terminals, easing the transition. **Use notebooks as a sandbox. Build castles in IDEs.**


## Give It a Shot

If you’ve never tried Jupyter notebooks (or variants like JupyterLab, Google Colab, or Observable for JavaScript), you’re missing a learning superpower. 
Install JupyterLab with `pip install jupyterlab` or try Colab for zero setup. They shine for rapid prototyping across languages, Python, R, C++ (via `xeus-cling`), even JavaScript.

But here’s what I wish someone had told me earlier: the best developers I’ve worked with weren’t the ones who knew everything from the start. They were the ones curious enough to admit what they didn’t know and flexible enough to adopt new tools when it made sense.

For most of my career, I built a career without notebooks, spanning L-systems, production pipelines, tech-sales bridging, and TD work at major studios. I was productive. Successful, even. But I was also working harder than I needed to on certain problems. Notebooks didn’t make me a better developer overnight, but they removed friction from the learning process. They gave me permission to be a beginner again, to ask “stupid questions” and test hunches without ceremony.

The irony? I spent years helping bridge communication between developers and creative teams, translating between disciplines and making complex systems accessible. And somehow, I missed that notebooks were doing exactly that for code: making it more accessible, more conversational, more human.

You don’t need a CS degree to excel as a developer. You need curiosity, persistence, and the wisdom to recognize when a tool can amplify your strengths. For me, that tool was Jupyter notebooks. For you, it might be something else entirely. The key is staying humble enough to try things that seem “beneath you” or “not for people like me.”

So explore freely in your notebook sandbox. Build those messy, beautiful, mistake-filled learning artifacts. Document your confusion. Celebrate your eureka moments. Then, when you’re ready, take what you’ve learned and build something real. Something that ships. Something that matters.

That’s where the magic happens.

The sandcastles you build today become the blueprints for the cathedrals you design tomorrow.


