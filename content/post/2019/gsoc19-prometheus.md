---
title: "GSoC'19 Report - CNCF/Prometheus"
date: 2019-08-26T19:01:08+05:30
---

This blogpost summarizes the work done by me in the summer of 2019 as part of Google Summer of Code under the organization [CNCF/Prometheus](https://prometheus.io/), I plan to keep contributing to Prometheus as a regular contributor after GSoC period.

> Huge thanks [Krasi Georgiev(Mentor)](https://github.com/krasi-georgiev) for being extremely helpful and supportive throughout this SoC. Most of the changes mentioned in this blogpost are not deployed to `prombench.prometheus.io` yet

Some silly Prometheus metrics, just for fun 😃 :
```
> gsoc_info{student="geekodour"}[12w]
> gsoc_info{student="geekodour",issues="9",prs="48"}
```

- Here's the readonly [link to my proposal.](https://docs.google.com/document/d/1vaROPSdbpUOFWNwNzy40kq0ZfOic7AhGLOb7_EELrZk/edit?usp=sharing)
- Here's the readonly [link to the doc my mentor and I used to keep things in sync.](https://docs.google.com/document/d/1200omTWeuhVEm-ubftW8g-7rb_YJM4ag9MyLNJ5o7iw/edit?usp=sharing)
- [All PRs and Issues in Prombench](https://github.com/prometheus/prombench/pulls?utf8=%E2%9C%93&q=author%3Ageekodour+)

Just to recap, here are the tasks that were proposed to be worked on, but during the coding period I ended up working on various other issues due to which I might have missed some of the proposed goals:

#### [prometheus/prometheus](https://github.com/prometheus/prometheus)
- **Adding [rule formatting](https://github.com/prometheus/prometheus/issues/21)**:
I initially started on this, but quickly switched to working on prombench related issues because there was a lot of things to be done in the prombench project. The only work done related to this was [adding yamlv3 to blackbox_exporter](https://github.com/prometheus/blackbox_exporter/pull/457), I plan to resume working on this as soon as all this related to prombench are stable.

#### [prometheus/prombench](https://github.com/prometheus/prombench)
- **Deployment logs with Loki**: [Issue](https://github.com/prometheus/prombench/issues/157) | [PR](https://github.com/prometheus/prombench/pull/241)

    Added Loki to Grafana, so Prombench users will now be able to look at the logs directly if they want to investigate more on that spike^.

    ![](https://user-images.githubusercontent.com/12918431/63013929-fa9fb880-beaa-11e9-8924-980fa188f8f7.png)
- **Scalability tests**:
    Due to other issues to work on, we could only disscuss a little about how to go about implementing this, this still remains a high priority issue. 
    - [Draft Doc](https://docs.google.com/document/d/1NJgomx_zjQMX9tJA4UZM0jtfQg1L8it3cQbtOcYXFHU/edit)
- **Adding race enabled tests**: Could not start on this at all, due to time.

#### Things didn't go as planned 🕥
Things didn't go as planned in the proposal. The main goal with Prombench was to improve it infrastruture wise and enabling Prometheus developers to get more out of it and fix pending issues, there has been significant progress for this primary goal.

The 🐘 elephant in the room was to switch to [GitHub Actions](https://github.com/features/actions) from [Prow](https://github.com/kubernetes/test-infra/tree/master/prow). This decision was taken because, even though Prow is an amazing CI system, it needed additional maintenance work and had a lot of moving parts.

Prombench currently runs a version of Prow that's about one year old, using a [custom built hook plugin: benchmark.go](https://github.com/prometheus/test-infra/blob/master/prow/plugins/benchmark/benchmark.go), I spent some time writing an external plugin for Prow which did not come of any use because we decided to switch to GitHub Actions anyway.

#### New Tools ✂️

With GitHub Actions we are no longer using that plugin, instead we built [commentMonitor](https://github.com/prometheus/prombench/tree/master/tools/commentMonitor) which I believe is an excellent tool for any project using GitHub Actions. It extracts the necessary variables needed by prombench.

Also added one missing part from Prombench, the [Alertmanager](https://github.com/prometheus/alertmanager). Since there's no direct Github Reviver for AM, built a webhook server [amGithubNotifier](https://github.com/prometheus/prombench/tree/master/tools/amGithubNotifier) which takes in an alert and sends it to the respective PR on GitHub as comments. We're using this to send alerts when any test is running for more than 3 days to save some cost.

#### New way to run Prombench tests 🌌
Looking at how the Kubernetes team is running their test-infra, I thought it would be good to try [running Prombench tests as a `StatefulSet`](https://github.com/prometheus/prombench/pull/248), it turned out to be great! Now Prombench supports **restarts** with different version and with newer commits with the same command.

#### Oh Github Actions. 🎬
So after all this, we were ready to test everything we did with Github Actions but the real 
🧱 brick on the head 🤕 fell on 9th of August'19  when GitHub decided to roll out a compeletely new Github Actions. Heck, they changed the expression language to YAML from HCL. It has quite a few bugs in it but I am not complaining because it is still in beta and the currently running features serves the usecases for Prombench.

So we had to rewrite everything related to GitHub Actions and currently we're testing Github Actions and Prombench and are getting promising results. We plan to move this to `prombench.prometheus.io` soon.

#### Some miscellaneous additions to Prombench:

- Grafana, Prometheus updated, Alertmanager added
- Changed directory structure and updated docs
- commentMonitor and amGithubNotifier
- Loki logs
- Running prombench tests as StatefulSets

Well that was all I did for GSoC :smile: