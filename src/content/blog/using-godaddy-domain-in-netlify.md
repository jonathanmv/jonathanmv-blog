---
title: "Using GoDaddy's domain to serve Netlify Website"
description: "A short blog post about a blog published on netlify and how to serve it in a GoDaddy domain"
pubDate: "Apr 20 2024"
heroImage: "/blog-placeholder-2.jpg"
---

We followed the [tutorial](https://docs.astro.build/en/tutorial/0-introduction/) to create a blog post using astro and to [deploy it using netlify](https://docs.astro.build/en/tutorial/1-setup/5/). That gave us a netlify address: `jonathanmv.netlify.app`. However, we wanted to serve the blog from the [jonathanmv.com](https://jonathanmv.com) domain, which I had already set up with GoDaddy.

First, we went to the [jonathanmv domain management in netlify](https://app.netlify.com/sites/jonathanmv/domain-management). The name of my website in netlify is `jonathanmv`. You need to change the name in the url to match yours.

Then I had to click a button saying `Add Domain` and type my domain. Finally, I had to click a link saying `Awaiting External DNS`. That showed a list of nameserver names that I had to change in GoDaddy.

The list is shown below. Please make sure you copy the values directly from Netlify instead of using these ones.

```
dns1.p09.nsone.net
dns2.p09.nsone.net
dns3.p09.nsone.net
dns4.p09.nsone.net
```

Now that we have the nameserver addresses we had to go to GoDaddy and change the DNS Nameservers there. I had to navigate to `Portfolio > jonathanmv.com > DNS > Nameservers` and click on the `Change Nameservers` button.

By default, it shows only two textboxes but you can add more to use the full 4-item list that netlify provides you. After giving the values you can save and you need to wait for the DNS propagration.

We used [DNS Checker](https://dnschecker.org/#NS/jonathanmv.com) to validate that the records were propagated. Although it showed that the records were propagated, I had to reset my DNS Cache for me to see the website loading from my own browser. Without resetting the cache, it was showing me the old GoDaddy nameservers.

You can check your DNS Lookup with the following command:

```bash
$ nslookup -q=NS jonathanmv.com
```

And you can reset your local DNS resolver (clear you DNS Cache) with the following command (it asks for your password):

```bash
$ dscacheutil -flushcache; sudo killall -HUP mDNSResponder
```

After clearing my DNS Cache, I went to my browser and reloaded the webpage. I saw that [my astro blog](https://jonathanmv.com) deployed using netlify was now loading from my domain parked on GoDaddy.
