---
title: In-toto 2020 Progress Report
date: 2020-12-07
categories:
- Projects
---

A summary of my contribution during the summer and fall semesters

# in-toto  

I added a flag that accepts a target directory to put generated metadata in, and I also provided related unit tests. Default is the current working directory.  
Behaviors:  
<pre>
    <code>
        $ in-toto-run <...> --metadata-directory --  
        $ in-toto-record stop <...> --metadata-directory --  
    </code>
</pre>
[https://github.com/in-toto/in-toto/pull/364](https://github.com/in-toto/in-toto/pull/364)  

# in-toto layout web tool  

I fixed the codebase's dependency issues by migrating it to the latest Flask version. I also added support for python3. Details are listed in the commit messages.  
[https://github.com/in-toto/layout-web-tool/pull/43](https://github.com/in-toto/layout-web-tool/pull/43)  
[https://github.com/in-toto/layout-web-tool/pull/51](https://github.com/in-toto/layout-web-tool/pull/51)  

I worked with Isha, another undergraduate student, on issue#6 (Enhance layout creation). We added a new function to the layout-web-tool. Given two 'snapshots' of a file structure, 'before' and 'after', this function will return which files were unchanged, modified, added, or removed. With this new feature, we can improve the algorithm for creating an in-toto layout. Besides, we also set up Travis for this repo and added related unit tests.  
[https://github.com/in-toto/layout-web-tool/pull/46](https://github.com/in-toto/layout-web-tool/pull/46)  

After finishing the above feature, I brainstormed some ideas that can further enhance the layout creation tool in the issue below.  
[https://github.com/in-toto/layout-web-tool/issues/48#issuecomment-670621099](https://github.com/in-toto/layout-web-tool/issues/48#issuecomment-670621099)  

After discussing with other in-toto members, I started working on improving the artifacts rules generation function. The original function only generates MATCH rules and ALLOW rules. It simply adds MATCH rules for all available materials and adds ALLOW rules for all artifacts. I reimplemented the rule generation function and added more complexity. Now, the function only adds MATCH rules for materials from the previous step(link). With the new features that Isha and I created, I added the functionality of generating DELETE, MODIFY, and CREATE rules. And of course, I added related unit tests. I also compared the run time efficiency with different data structures and chose a suitable one.  
[https://github.com/in-toto/layout-web-tool/pull/49](https://github.com/in-toto/layout-web-tool/pull/49)  

While I was working on enhancing the rules generation functions, Lukas discovered an interesting rule conflicting problem: when we delete an artifact from a previous step(link), the DELETE rule will not has an effect because of a previous MATCH rule. I opened an issue about this problem. For now, as Lukas recommended, I added a warning in the code that will warn for any delete rule that has no effect.  
[https://github.com/in-toto/docs/issues/41](https://github.com/in-toto/docs/issues/41)  

# Miscellaneous  

In-toto uses the Google-style docstring format and the Napoleon Sphinx extension to auto-generate HTML docs. However, we still use a custom 'Side Effects' docstring section, which does not render nicely. I post some ideas and implemented some possible solutions for rendering custom docstring. I also reached out to Napoleon maintainer to see if they are interested in a PR that can solve this problem.  
[https://github.com/in-toto/in-toto/issues/401](https://github.com/in-toto/in-toto/issues/401)  

This issue asks for a feature that can show output diff for in-toto demo test failures. I plan to finish this by the final week.  
[https://github.com/in-toto/demo/issues/16](https://github.com/in-toto/demo/issues/16)  