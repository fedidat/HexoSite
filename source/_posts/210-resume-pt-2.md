---
title: Making an automated resume (part 2)
date: 2018-05-28 20:09:45
tags:
---

In [the first part](/150-resume/), I talked about the situation of automatic resume generation.

Over 3 weeks afterwards, I'm finally done with testing the different existing setups, and tweaking them for my needs. My requirement that turned out to be the most problematic is the need to work for both text directions (left-to-right and right-to-left), along with conversion to DOC format. This won't affect most people but I still think my proposed solution is the simplest and adapted to most use cases.

## The solution

I ended up experimenting a lot with various repositories on Github. As I mentioned in part 1, I was set on Mark Szepieniec's [pandoc_resume](https://github.com/mszep/pandoc_resume) as my starting point.

Then, as it showed a number of shortcomings for my needs, I experimented with other repos, including Sonya Sawtelle's [markdown-resume](https://github.com/sdsawtelle/markdown-resume), John Didion's [cv](https://github.com/jdidion/cv), Bradley Rastrullo's ["resume"](https://github.com/brastrullo/resume) and Matheus Albuquerque's ["resume"](https://github.com/ythecombinator).

Then I found the simplest and most flexible solution: Jack Senechal's [resume](https://github.com/jacksenechal/resume). It converts the Markdown source to HTML5 and ODT via pandoc. And then it harnesses the power of Libreoffice headlessly to convert the ODT to PDF and DOC. Compared to other systems I've seen, its simple bash script is a beauty.

The only downside to all pandoc-based generation methods is the integration. Consider my Hexo blog, built from source with npm by Netlify. Their nodes will never have pandoc or headless Libreoffice installed, and it can't be shipped as a node module. So the only thing I can do is setup a script that generates the documents locally and copies them to my local Hexo repo. That's manual and not at all in the spirit of automation. But until HackMyResume supports right-to-left, that's my only choice.

## The result

At the time of writing, you can see my resume on my [about](/about) page in all three languages (English, French and Hebrew), and in three formats: online (HTML+CSS), PDF and DOC. You can also head to my [repo](https://github.com/fedidat/resume).

It may look simple - too simple. But there are a few reasons for this: 

* I hate clutter. Decorations, bubbles, colors... they all distract from the information about you. It just looks flashy and obnoxious to me. Sober and straight to the point looks best.

* Fancy markup screws with computers, both with generation and with automatic parsing, that a lot of companies still use nowadays. Look consistency may also be a problem. It's fine for HTML since the styling is separated from the markup (as long as there's little Javascript to confuse search engines). But for PDF and DOC I wouldn't advise it.

I know that most front-end designers feel that they have to show off their web skills in their resume. But although I think generating a resume isn't a good idea in that case, my solution is also suitable for that, by customizing the header and footer for the HTML conversion with CSS and JS.

## What I learned

It may seem futile to spend 15 hours on generating your resume, but as in many cases, it's all about the journey. I ended up learning a bit about Haskell through pandoc, going back to LaTeX and discovering about ConTeXt (an extension of TeX with advanced typography features). It also gave me the occasion to dive deep into writing Makefiles and into understanding Libreoffice Writer.

The point is, even if a programming challenge may seem superfluous, you may end up learning much more that you would think looking at the end result.

Anyway, time will tell if my generation strategy with the sober layout will win employers over!

*Note: Once again, credit goes to Jack Senechal for his [implementation](https://github.com/jacksenechal/resume) of the pandoc resume.*