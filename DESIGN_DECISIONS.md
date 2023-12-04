# Bitnami Helm charts design decisions

The following document is meant to log all reasoning and design decisions with respect to the Bitnami Helm charts.

Use the following format for contributing to this document:

```
  ## YYYY-MM-DD: <Title for the design decision>

  **TL;DR:** <brief summary>

  <Reasoning and explanation, putting any related issues and PRs (if any)>
```

## 2021-02-25: Add diagnostic mode

**TL;DR:** We added a new common value to our charts that enables a "Diagnostic mode", which will deploy all pods with an empty command and with all probes disabled. The aim of this mode is to ease debugging.

Checking our support cases, we found that users found extremely difficult to debug and troubleshoot cases where deployments (especially infrastructure ones) got corrupted and ended in a infinite CrashLoopBackOff loop. In this kind of scenarios, it is mandatory to enter the containers and manually fix the installations. In order to ease that, we enabled a diagnostic mode that will deploy the installation without probes and with a "sleep infinity" command. Thanks to this, users will be able to easily access and fix their broken installations.

## 2021-02-25: Replace `bitnami/minideb` by `bitnami/bitnami-shell`

**TL;DR:** [`bitnami/minideb`](https://github.com/bitnami/minideb) is not used on auxiliar containers (such as init containers or sidecar containers) anymore. [`bitnami/bitnami-shell`](https://github.com/bitnami/containers/tree/main/bitnami/bitnami-shell) will be used from now on, instead.

The [`bitnami/minideb`](https://github.com/bitnami/minideb) image started being a good fit for auxiliar containers (see [why use minideb](https://github.com/bitnami/minideb#why-use-minideb) section).
However, several of these containers required non trivial packages to be present (e.g. `sysctl`). We can argue that many of these requirements would be a good-to-have. Nevertheless, we aim to keep [`bitnami/minideb`](https://github.com/bitnami/minideb) very light-weight and, more important, with a very small vulnerability surface.

Hence, instead of adding this good-to-have delta to [`bitnami/minideb`](https://github.com/bitnami/minideb), we have developed [`bitnami/bitnami-shell`](https://github.com/bitnami/containers/tree/main/bitnami/bitnami-shell). This allows us to keep `minideb` small and focused, which is a good quality to build other images on top, while our charts can use an enriched container with useful system packages or useful shell scripts.

## 2021-01-20: Removal of _values-production.yaml_ files

**TL;DR:** The _values-production.yaml_ files are not supported anymore in favor of providing flexibility and documentation to customize the default _values.yaml_

Some of the Bitnami Helm Charts (40 of 80) contain a _values-production.yaml_ file apart from the _values.yaml_. The content of both values files is practically the same but the production ones have different default values in some parameters, being the most common metrics enabled, number of replicas, forced to specify a password, forced a URL, TLS enabled, and more.

There are no clear guidelines as to what is considered "production", as this concept of "production" varies according to the use case or need of each user. This is the principal reason why the Bitnami team is planning to remove the _values-production.yaml_ file in favor of providing flexibility and documentation so each user can customize the default _values.yaml_ to their particular needs.

Apart from that, there are other reasons behind this decision such as the lack of proper tests covering custom values files like the _values_production.yaml_, difficulty to maintain in sync _values.yaml_ and _values-production.yaml_, need to place the values file in the host or specify it via URL, among others.

**Issue:** https://github.com/bitnami/charts/issues/5095

## 2021-01-20: Creation of the design decisions document

**TL;DR:** In order to improve the transparency as well as making the onboarding easier for contributors, we decided to create this document.

**PR:** https://github.com/bitnami/charts-docs/pull/2
