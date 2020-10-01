# Codemagic

## Overview

Currently Codemagic only provides a UI for Flutter builds. It can build anything else, but requires a custom config file. The config file must be located in the `master` root directory and named `codemagic.yaml`

## Encrypted Values

For code signing and deployment the config file must contain sensitive credentials. These should never be entered as plain text. Codemagic provides an easy way to encrypt the values.

In Codemagic, navigate to your app, you should find a link titled `Encrypt environment variables`. You can encrypt both strings and files. Encrypted values look like this: `Encrypted(YOUR_ENCRYPTED_VALUE)`

## Workflows

Most exmaples here are split into one workflow per example. In a single `codemagic.yaml` file you can have multiple workflows.

## Default Values

These are examples only. You should go through each line and make any nescasary changes.
