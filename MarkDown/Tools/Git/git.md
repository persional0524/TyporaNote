```shell
ssh-keygen -t ed25519 -C "495350559@qq.com"


Host *
  AddKeysToAgent yes
  UseKeychain yes
  IdentityFile ~/.ssh/id_ed25519ßß
```