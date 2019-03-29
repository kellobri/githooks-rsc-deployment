# Git Pre-Commit Hook Examples for RStudio Connect Deployments


## Basic examples

Passive example: Print a RED reminder message on every git commit

- Pros: Non-blocking
- Cons: Easily ignored

```
#!/bin/sh

tput setaf 1; echo "# Please remember to update the manifest.json file!"
tput sgr0
exit 0
```

Block any commit that does not include a modified manifest file

- Pros: Effective behavior modification
- Cons: Annoying, because not all commits truly require an update to the manifest file

```
#!/bin/sh

modified=`git status -s`
manifest=`git status -s | grep 'application-dir/manifest.json'`

if [ -n "$modified" ] &&  [ -z "$manifest" ]; then
    tput setaf 1; echo "COMMIT BLOCKED: please update the manifest with rsconnect::writeManifest()"
    tput sgr0
    exit 1
fi

exit 0
```

## Application directory example

```
#!/bin/sh

if git diff --cached --name-only | grep --quiet "application-dir/"
then
  modified=`git status -s | grep 'M.*application-dir\/.*'`
  manifest=`git status -s | grep 'application-dir/manifest.json'`

  if [ -n "$modified" ] &&  [ -z "$manifest" ]; then
      echo "ISSUE FOUND: It looks like you updated files in your application directory without re-running rsconnect::writeManifest()"
      exit 1
  fi
fi

exit 0
```