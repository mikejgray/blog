---
title: "{{ replace .Name "-" " " | title }}"
date: {{ .Date }}
draft: true

slug: {{ .File.BaseFileName }} # Will take the filename as the slug.

summary: "" # Remove this if you want Hugo to just use the first 70 (configurable) characters of the post as the summary.
description: ""

# Lists
keywords:
tags:
categories:
---
