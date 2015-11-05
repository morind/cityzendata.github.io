---
layout:     post
title:      "Dealing with ephemeral time series"
subtitle:   "best practices fo escaping hell"
author:     "hbs"
---
### Intro ###

At Cityzen Data we are convinced that time series are the best way to monitor your IT infrastructure, sharing this conviction with others like <a href="https://blog.twitter.com/2013/observability-at-twitter">Twitter</a>, <a href="http://www.vldb.org/pvldb/vol8/p1816-teller.pdf">Facebook</a> or <a href="https://www.reddit.com/r/IAmA/comments/177267/we_are_the_google_site_reliability_team_we_make/c82y43e">Google</a>.

The advent of cloud infrastructures and the overall containerization trend we observe today lead to infrastructures that are more and more elastic, meaning we have services which come and go and we increase the nesting levels.

This nesting induces multiple levels of metrics. The lowest level is that of the bare metal, then comes the containerization level, then the containers themselves, then the services within the containers.

It is legitimate to want to collect metrics at all those levels, but given the dynamic nature of the containers this can rapidly lead to a situation where you have to handle many time series which are short lived. Those short lived time series are usually associated with a random container id which is generated when an instance is spawned. While it makes perfect sense to store the container id in a label identifying a time series, one can question the manageability of all those series in the mid to long term.

Most of the <a href="https://en.wikipedia.org/wiki/Time_series_database">time series databases</a> out there will suffer when they have to manage millions or billions of metrics, and the usefulness of keeping so many short lived time series is also questionable. 

That is the reason why we give our customers a set of rules to follow so they can benefit from the short term value of ephemeral time series while not clogging their overall time series corpus.

### Our proposed rules ###

Those rules are mentioned below, feel free to react to them on <a href="https://twitter.com/CityzenData">Twitter</a>, we'll be happy to discuss them:

* Be wise when you associate ephemeral identifiers (such as container id) to time series

The growing popularity of systems such as <a href="">docker</a> has had the side effect of making random container ids appear in time series labels. While this makes perfect sense if the time series you are tracking are strictly related to the given container, very often we see such labels being associated with time series which have a meaning well beyong the container in which they operate.

Our advice is therefore to associate the ephemeral container id only to the metrics of the container and use an identifier which will be reused across container spawns for the time series of the systems executing within the container. In the case of docker, <a href="https://github.com/docker/docker/issues/1">naming</a> your containers in a stable way seems the best option, use names such as nginx-1, nginx-2, ... for multiple instances you may run, and use the name instead of the uuid wherever you can.

By following this advice you will be able to manipulate those metrics across container executions.

* Associate a 'ttl' label to those ephemeral time series

Time series associated with a given container id will generally have a very low value after some time, so our advice is to label them with a 'ttl' so the time series which were created around a certain time can be easily identified.

By adding a label value of 'YYYY-MM-DD' which is the date at which the container was started for example you can easily find all those time series which no longer provide much value.


* Purge your ephemeral time series on a regular basis

As stated in the introduction, the idea is to remove from your corpus the time series you no longer need. This covers the datapoints themselves but more importantly the metadata describing the time series, so for example retention policies alone do not solve the problem as they will usually ony suppress the datapoints which are older than a threshold.

What you need to do is to remove permanently everything related to ephemeral time series you no longer need. So given you have followed carefully the previous advice, it is very easy to issue a deletion command selecting all time series with a given value of your 'ttl' label. This deletion will therefore remove the datapoints and the metadata of the associated time series. For archival purpose you can perfectly do an export of those data before deleting them from your time series datastore, to store them in HDFS for example.


### Beyond containers ###

The most frequent source of ephemeral ids we see today is undoubtebly the containerization of IT, but the rules described above can also be applied to other ephemeral situations. Tracking technical parameters of phone calls is another example of ephemeral time series.

We hope that those simple rules will help you deal with you time series better. And if your actual time series database does not offer sufficient features, do not wait any longer, contact us!
