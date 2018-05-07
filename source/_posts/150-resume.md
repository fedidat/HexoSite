---
title: Making an automated resume (part 1)
date: 2018-05-06 08:30:44
tags:
---

Resumes are a notorious pain point for developers. 
Nowadays we are accustomed to everything being automated and version-controlled,
from build systems with Jenkinsfile and similar, to the very application servers 
with Spring Boot, package.json, Kubernetes and so on.
Yet somehow resumes have still escaped this trend, and as far as I could tell
there is not one out-of-the-box FOSS tool that could at least generate a DOC file.

As usual, I'll make a top-down summary of the options available in 2018
in the eyes of a programmer, then I'll detail what I choose and how I implemented it.

## 1. The advantages and requirements

- Version-controlled: The main requirement is the ability to version-control the CV.  
That way you can keep track of the progression of your layout and content easily, as well as restore older content and never again lose older versions in your filesystem or on your old hard drive.   
More importantly, this gives you a single source of truth as you know there is only ONE repository in the world that contains your up-to-date document.

- Formats: Another advantage of a good tool is to easily generate some required formats for all to see or process. Ideally, you would want as many as possible, but I consider some necessary: 
    - DOC or DOCX: some older HR departments still require these for automatic processing,
    - PDF: the standard file that most companies will want to receive attached to an email,
    - Web: people browsing your website will most likely want to see this. 

- Features: 
    - Specific styling can make your resume minimalistic, elegant or unique, and having a consistent style across formats may be desirable.
    - And text direction (LTR or RTL) may be a requirement for some - including myself. 

## 2. The options

- **Word processor and Notepad**: This is derived from the conventional approach of writing your resume in a word processor, ideally Microsoft Office for compatibility, and exporting to various formats. But in addition, you would copy everything and dump it in text and HTML files, with the latter backed by a custom CSS style.  
This most likely requires some manual steps beyond save-and-commit, and is generally more cumbersome. But it is flexible, it does enable version-control, keep consistent styling and fill the formats requirement.  
This will be the baseline and we'll do our best to replace this.

- **LaTeX**: Those who are not afraid of getting their hands dirty will prefer this option: straight up just working on a .tex file and later converting to other formats via pandoc, convert or other commands. Most flexible approach but not easy to setup or to operate.

- **Commercial (and proprietary) options**: 
    - [Resumonk](https://www.resumonk.com/): This is a specialized platform that enables version history, online editing and format exporting based on LaTeX. The free tier is crippling, but my main problem is that version controlling is not done through git but through their proprietary platform. There is no way to migrate either.
    - [VisualCV](https://www.visualcv.com/): Another specialized platform, and the exact same problems and pricing tiers. For the very noobiest.  
    Their pricing is hard to access, so here it is: $12/month (3 months minimum) gets you "Access all CV and web designs, Multiple resume versions, Export as PDF & Google Doc, No VisualCV branding, Advanced CV tracking, Personal domain name).

- **Markdown+Pandoc**: This has been a popular approach for a few years. The quick rundown is [here](https://mszep.github.io/pandoc_resume/). Pandoc is an aspiring universal document converter that has its quirks, and using Markdown as the central source results in the smoothest possible day-to-day operation. The problem is with the initial setup. The pseudo-magical nature of Pandoc often results in unexpected behavior, in particular with styling, layout and direction.

- [**JSON resume**](https://jsonresume.org/): Around the same time as the Markdown+Pandoc approach started, the JSON resume took off with the project of offering very smooth and predictable setup and operation. The downside is the whole thing is very linear, and everyone uses the same theme with the same outcome. But these are not problems for most people. My issue with this the JSON resume is that exporting to DOCX is not possible.

- [**HackMyResume**](https://github.com/hacksalot/HackMyResume): Same as the JSON resume (linear, predictable, JSON format) but the CLI is smart, works out-of-the-box and generates tons of formats including DOC, provided that the theme you're using supports that format. It also has many other features. It doesn't work well with right-to-left however.

As you can tell, no solution is really satisfying. 
After a few days of attempting with great difficulty to implement 
a solution that satisfies all requirements, it seems that the most flexible and standard approach seems to be Markdown and Pandoc.

I'll continue to work towards this and hopefully share my solution here and on Github within the next few days.