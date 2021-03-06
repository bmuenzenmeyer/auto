---
image: https://cdn-images-1.medium.com/max/800/1*TVSbt7qsyleNiQ84la_log.jpeg
title: Death to Monotony
author:
  name: Adam Dierkens
  url: https://github.intuit.com/adierkens
---

# How auto can “handle the rest for you”

I’ve been a front-end engineer at Intuit in San Diego for the past three years. As one of three members of Intuit’s Player team, we are responsible for delivering a JavaScript framework dedicated to rendering most of a user’s experience in [TurboTax](https://turbotax.intuit.com/). We end up releasing a lot of code, usually React components, quite frequently to our internal node package manager (npm) instance. It’s not uncommon for us to hit double-digit releases in a single day. This means lots of version bumps, release notes, changelogs, Jira tickets, Slack announcements, and all the other goodies that come with communicating to dependent teams, that their bug is now fixed, or a new feature is ready for them to use. While none of these steps are intrinsically difficult, the time commitment adds up quickly, so we wrote auto: a tool to [auto-generate releases based on semantic version labels](https://github.com/intuit/auto).

---

## The Problem

Historically, our release process involved a lot of manual labor. Before publishing an update to npm, someone on the team had to gather all of the changes merged since our last release, write up a changelog, and figure out what the next version was going to be. This often involved a lot of back and forth between GitHub and Jira. Once we had all of that, we could create a release, publish it to npm, document it in GitHub, and post an update to our team’s Slack channel. These 6–7 monotonous tasks were needed for each release, which easily took up to 30–60 minutes out of our day.

To distribute the pain, each month we would nominate someone as the release marshal, who inherited the responsibilities of handling all of the release tasks. It was an inefficient, joyless process that we all resented. Something needed to change.

---

## The Solution

Auto started as a simple Node script that made a few requests to the GitHub API. It gathered the commits merged from the last release and posted those to GitHub. We still had some manual steps in the release process, but it was the start of the end of the monotony.

I brought in another engineer on my team, [Andrew Lisowski](https://www.linkedin.com/in/andrew-lisowski-8b419977/), and we spent the next few months building out the script into a proper npm module. We gave it a well-defined API comprised of small atomic operations that could handle all parts of the release. With auto, the new process is entirely hands-off — simply hit “merge”, and everything happens automatically.

We initially crafted auto to solve for our team’s specific release process — which needed to post comments and statuses back to a pull request, generate release notes based on what was merged, and document these releases on GitHub and Slack. We eliminated the need for a dedicated release marshal, instead opting for each engineer to annotate their pull requests with a proper semantic version label. Auto would then take it from there, deploying and documenting the release without the need for manual intervention. After dogfooding auto internally for about six months, there was a lot of interest from outside teams, and we knew each of them had slightly different release pipelines, so we worked to make it reusable and customizable for anyone that wanted to use it.

Andrew and I had been talking about using TypeScript for a while and decided that this was a perfect project to start out. By bringing static type-checking to our JavaScript code, refactoring became much less painful, as types quickly caught would-be bugs much sooner. Since writing auto, we’ve made it a point to use TypeScript as our de facto language in any new project we create, including many of the front-end components used in TurboTax today.

Inspired by the power and modularity of webpack’s plugin architecture, we brought in their tap-able module, the core code that powers their plugins. Andrew and I spent about a month slowly refactoring the code to leverage the plugin-based architecture you see today. It went live on [GitHub](https://github.com/intuit/auto) in December 2018 and has since attracted several outside contributors.

Andrew was also getting more involved in the open source community at Intuit, and auto seemed like a great fit for a project to release next — any work that we’d need to do to make auto pluggable for teams inside of Intuit also meant that teams outside of Intuit could leverage it.

While a few libraries with similar capabilities already exist, we had yet to identify one that truly met our needs. Packages such as semantic-release were a great inspiration to auto-calculate a version bump based on merged changes, but require a significant change to a developer’s workflow, something we weren’t prepared to enforce.

Auto’s philosophy is to compose each part of the release pipeline into a series of plugins, allowing it to handle a bunch of different use-cases out of the box. Ultimately it boils down to two commands: “auto init” to get started and “auto shipit” to release. Anyone can write a plugin for any part of the release process and add their own custom handlers, without having to change the core system. If instead of publishing to npm, you want to publish an extension to the Chrome Web Store, simply enable that plugin and auto will handle the rest for you.

We were pleasantly surprised by how quickly auto was picked up by the community. It filled a niche that the existing libraries didn’t quite cover. By using labels — instead of specially formatted commit messages required by some existing solutions — we were able to significantly lower the barriers to entry for auto. Auto doesn’t impede the developer workflow — just throw a label on a pull request and continue to focus on code.

Working on auto from the start has been an amazing experience — not only did it help our team, but it’s awesome to see other teams integrating it into their releases. I’m really happy that we got to share it with the open source community and am excited to see where the future takes it next.
