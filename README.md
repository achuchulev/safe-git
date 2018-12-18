# safe-git

## AWS creds globally for git

- Enable git templates:
```
git config --global init.templatedir '~/.git-templates'
```

- Create a directory to hold the global hooks:
```
mkdir -p ~/.git-templates/hooks
```

- Create `pre-commit` hook in ~/.git-templates/hooks

`~/.git-templates/hooks/pre-commit`:

```
#!/bin/sh

FILES=$(git grep -E "(A3T[A-Z0-9]|AKIA|AGPA|AIDA|AROA|AIPA|ANPA|ANVA|ASIA)[A-Z0-9]{16}")
if [ $? -eq 0 ];then
  echo "err: commit include regex for AWS"
  echo ${FILES}
  echo "use git rm to remove files and avoid commiting your creds"
  echo ""

  for f in $FILES; do
    echo git rm --cached ${f%%:*}
  done

  echo ""
  exit 1
fi
```


- Make sure the hook is executable.
```
chmod a+x ~/.git-templates/hooks/pre-commit
```

- Re-initialize git in each existing repo you'd like to use this in:
```
git init
```



## AWS creds on git project, as hook

`.git/hooks/pre-commit` with the following content

```
#!/bin/sh

FILES=$(git grep -E "(A3T[A-Z0-9]|AKIA|AGPA|AIDA|AROA|AIPA|ANPA|ANVA|ASIA)[A-Z0-9]{16}")
if [ $? -eq 0 ];then
  echo "err: commit include regex for AWS"
  echo ${FILES}
  echo "use git rm to remove files and avoid commiting your creds"
  echo ""

  for f in $FILES; do
    echo git rm --cached ${f%%:*}
  done

  echo ""
  exit 1
fi
```

ensure the file have the right permissions

```
chmod +x .git/hooks/pre-commit
```

## AWS creds on local repo

To check for AWS creds manually, on a git repo, you can use.

```
git grep -E "(A3T[A-Z0-9]|AKIA|AGPA|AIDA|AROA|AIPA|ANPA|ANVA|ASIA)[A-Z0-9]{16}" $(git rev-list --all)
```

## truffleHog

### How to install

```
pip install truffleHog
```

### Scan local repo

```
trufflehog --regex --entropy false --rules <(curl -sL https://raw.githubusercontent.com/kikitux/safe-git/master/regexes.json) .
```

### Scan remote repo

```
trufflehog --regex --entropy false --rules <(curl -sL https://raw.githubusercontent.com/kikitux/safe-git/master/regexes.json) http://github.com/user/repo
```
