## Git commands
```bash
git log # Check logs
git log -p # Show patches
git ls-files # List files
git checkout -- . # Extract all files
git diff # Check changes
git show <commit-id> # Show specific commit
git checkout <commit-id> # Navigate specific commit
git cat-file -p master^{tree} # Show tree's filenames
git cat-file -p <file-id> # Cat file
git config --global user.name <user>; git config --global user.email <email> # Set git identity to commit files
git add -A; git commit -m <message> # Commit
GIT_SSH_COMMAND='ssh -i id_rsa' git clone git@<ip>:/git-server # Git shell interactions
```

## Extract all files
```bash
wget -r http://host.com
```

## References
- Hunit - Proving Grounds