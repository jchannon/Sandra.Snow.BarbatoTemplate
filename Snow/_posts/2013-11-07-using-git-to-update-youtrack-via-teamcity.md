---
layout: post
title: Using Git to update YouTrack via TeamCity
category: git, team city, youtrack
---

This post is mainly a reminder for me as I keep forgetting the command in Git to integrate commits to YouTrack items.

YouTrack uses TeamCity to get the information about the commits and then scans the commit comment for a YouTrack item id and any commands that it can apply such as item status or time spent on said item.

There is some documentation [here][1] but its not the greatest in terms of clarity and I've spoken to [Hadi Hariri][2] from JetBrains about improving this so hopefully they're working on it.

Anyhow here's some example Git commands to wire it all up

    git commit -am "I fixed a massive bug #PROJ-158 Complete"
    git commit -am "I fixed a massive bug #PROJ-158 Complete add work 1h"

The first command will update the status of YouTrack item #PROJ-158 to Complete.  The second item will do the same but also add Time Tracking information to the item in YouTrack.

Hope that helps, Happy Coding!

[1]: http://confluence.jetbrains.com/display/YTD4/Executing+Commands+from+Comment+to+VCS+Commit
[2]: https://twitter.com/hhariri
