# Multiple Login on GitHib

# Scenario

1. You have more than one github.com account
2. You want to be able to clone/commit/push into each account with different SSH keys
3. You already have your default SSH key setup as a key with one GH account.
4. Github doesn't let you use the same SSH key for two different Github accounts.

# Solution

1.  From your local shell, create a second SSH key:

```
$ ssh-keygen
```

Save this key to a **new filename** (don't clobber the existing one)

For example if your preexisting public key is `~/.ssh/id_rsa.pub` (and private
key is `~/.ssh/id_rsa`), then use a different name than `id_rsa`. 

Under the SSH keys section of the Second User account you have on GH, add
this new (second) key (eg: `foo_id_rsa.pub`) to your Second User account
on GH.

Example:
```
user@host:~$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/user/.ssh/id_rsa): /home/user/.ssh/foo_id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/user/.ssh/foo_id_rsa.
Your public key has been saved in /home/user/.ssh/foo_id_rsa.pub.
The key fingerprint is:
SHA256:03535whateverthisgoing_to_be_who_knows94342 user@host
The key's randomart image is:
+---[RSA 2048]----+
|..o.o.o.o.o.o.o. |
|    0  o+==00o   |
|       ooo. .    |
| o + -- . == +   |
|  ........       |
| * ............  |
|.....oo..whatever|
| .               |
|                 |
+----[SHA256]-----+
```

That new file `~/.ssh/foo_id_rsa.pub` is what is used to setup the SSH key for the
second GH account.   The choice is yours if you want to use a pass-phrase.

2.  Edit or create `~/.ssh/config`

Specify multiple Identities:

```
# My default github access using the id_rsa key pair
Host github.com
  HostName github.com
  User  git
  IdentityFile ~/.ssh/id_rsa

# My second github access using the new `foo_id_rsa` key pair
Host github-foo
   HostName github.com
   User git
   IdentityFile ~/.ssh/foo_id_rsa
```

3. Make (or acquire the URL) for cloning a repo from your new (second) GH account
repository.

When it's cloned, use the alias as defined in the `~/.ssh/config`

Eg: *DO NOT*

```
$ git clone git@github.com:second-user/repo.git
```

Instead do this:

```
$ git clone git@github-foo:second-user/repo.git
```

Notice we didn't specify the HOST `github.com`.  We specified the alias
for using `github.com` by the name `github-foo` which implies the use of
a *different Identity File*.


4.  Now, when you work with this repo, the configuration of the repo
locally will know the alias that is used to determine which host is 
accessed and thusly, which Identity File is used (the key)


