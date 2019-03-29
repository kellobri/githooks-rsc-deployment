# Git Pre-Commit Hook Examples for RStudio Connect Deployments


## Basic examples

Passive example: Print a reminder message on every git commit

- Pros: Non-blocking
- Cons: Easily ignored, hard to notice

```
#!/bin/sh

echo "# Please remember to update the manifest.json file!"
exit 0
```

Block any commit that does not include 

## Application directory examples

```
#!/bin/sh

if git diff --cached --name-only | grep --quiet "application-dir/"
then
  echo "Use rsconnect::writeManifest() to update the manifest.json file."
  exit 0
fi
```

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