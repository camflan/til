# Access github action runner in situ

When you need to get hands-on to debug a failing GitHub action, you can drop directly into the container using [this action](https://github.com/marketplace/actions/debugging-with-ssh). I have 2 snippets that I use,

### When I need to get into the container before a problematic job

I use this snippet quite a bit to drop into the container to debug paths/environment issues before a step fails so that I can manually walk through the steps. I also set some protections so that only the person who triggered the workflow or myself can access the session.

**Snippet:**

```yaml
---
- name: Setup upterm session
  uses: lhotari/action-upterm@v1
  with:
    ## limits ssh access and adds the ssh public key for the user which triggered the workflow
    limit-access-to-actor: true
    ## limits ssh access and adds the ssh public keys of the listed GitHub users
    limit-access-to-users: camflan
    ## If no one connects after 2 minutes, shut down server.
    wait-timeout-minutes: 2
```

**Example usage:**

```yaml
---
name: My problematic CI workflow
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2

      ################ COPY STARTING HERE  #####################
      - name: Setup upterm session
        uses: lhotari/action-upterm@v1
        with:
        ## limits ssh access and adds the ssh public key for the user which triggered the workflow
          limit-access-to-actor: true
        ## limits ssh access and adds the ssh public keys of the listed GitHub users
          limit-access-to-users: camflan
        ## If no one connects after 2 minutes, shut down server.
          wait-timeout-minutes: 2
      ################### END COPY HERE  #######################

      - uses: actions/something-fails-here
```

### When a workflow fails

Putting this at the end of a workflow will give you access to any failing action for 2 minutes.

**Snippet:**

```yaml
---
- name: Setup upterm session
  uses: lhotari/action-upterm@v1
  if: ${{ failure() }}
  with:
    ## limits ssh access and adds the ssh public key for the user which triggered the workflow
    limit-access-to-actor: true
    ## If no one connects after 2 minutes, shut down server.
    wait-timeout-minutes: 2
```

**Example usage:**

```yaml
---
name: My failing CI workflow
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2

      ################ COPY STARTING HERE  #####################
      - name: Setup upterm session
        uses: lhotari/action-upterm@v1
        if: ${{ failure() }}
        with:
        ## limits ssh access and adds the ssh public key for the user which triggered the workflow
          limit-access-to-actor: true
        ## If no one connects after 2 minutes, shut down server.
          wait-timeout-minutes: 2
```
