---
author: Andrew Cole
date: 2020-01-01
category:
- geek
- life
title: Happy New Year 2020
draft: True
---

What did I do for New Years Eve 2019? I resurrected this blog, that’s what.

\n\n\n\n

For much of the time since the [last post](\"https://illallangi.com/2018/09/28/modern-infrastructure/\") (remember that, when I was rushing headlong in to Microsoft’s arms and reengineering everything to work on Azure), this blog has been down. When it hasn’t been 404’ing or just plain not resolving, [illallangi.com](\"https://illallangi.com\") has been a simple page, like you write in first year HTML:

\n\n\n\n

    <html>\n <head>\n  <title>illallangi.com</title>\n </head>\n <body>\n  <h1>illallangi.com</h1>\n  <hr/>\n  <p>Just another vanity domain...</p>\n </body>\n</html>

\n\n\n\n

While working on migrating from an old to a new server over at [hetzner](\"https://hetzner.de\"), to get everything upgraded to [Fedora 31](\"https://getfedora.org/en/server/\") for consistencies sake, I came across a backup of this site. Cue several hours of futzing with MariaDB, WordPress and PHPMyAdmin, interrupted only by [some rude person setting off fireworks](\"https://www.abc.net.au/news/2019-12-31/new-years-eve-melbourne-2019-to-2020-fireworks/11819010\"). One [GitHub pull request](\"https://github.com/illallangi-kubernetes/syd-he0/pull/4\") later and we’re back baby!

\n\n\n\n

Note that bit about one GitHub pull request. Over the Christmas break this year I’ve been a bit of a hermit (after smashing the car up), but I’ve finally got Kubernetes running, integrated in a devops- like way with some GitHub repositories. I hope to document the whole ball of wax here, but we’ve all [heard](\"https://illallangi.com/2015/07/14/giving-it-another-try/\") [that](\"https://illallangi.com/2015/07/18/server-setup/\") [before](\"https://illallangi.com/2018/09/28/modern-infrastructure/\").

\n\n\n\n

And now it’s 0230, and I promised myself I’d start to get my sleep pattern approaching normal tonight…

\n\n\n\n

_Give the certificate issue some time, I’ve managed to hit [LetsEncrypt](\"https://letsencrypt.com\")‘s [rate limiting](\"https://letsencrypt.org/docs/rate-limits/\") during my futzing around._